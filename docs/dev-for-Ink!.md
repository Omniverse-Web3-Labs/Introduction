# Dev-Introduction for Ink!

## Introduction

The underlying principle of `O-DLT` is a little bit abstractive, but the implementation of `Ink!` makes sense.

No matter whether ERC-20-like or ERC-721-like assets, the key point of them are the states transferring operation, like the `transfer`, `transferFrom`, and `mint/burn`. And the main philosophy of the `O-DLT` token is the states' synchronization between different chains. Once these operations happen on one chain, the related states’ changes need to be synchronized to the member chains of the token. But how to make the synchronization efficient and absolutely trustless?  

The tricky to solve it is not esoteric, and it can come from the basic cryptographical method, that is, we do not need to carry the changes from one to another, which is very complex to be made out, but carry the transactions along with signatures instead. As the signature cannot be denied and it is verifiable, so if we can verify a transaction in the smart contract on one chain, we can also verify it in the smart contract (or smart-contract-like mechanisms. i.g. `Substrate-Pallets`) on another chain. 

The native interface like `transferFrom` in ERC-20 and ERC-721 does not satisfy, as there are no signatures as the input. So we need to define additional interfaces. All the newly defined interfaces about the `O-DLT` assets are presented in the related [EIP](https://eips.ethereum.org/EIPS/eip-6358). For the `Ink!` smart contract version, a typical example can be found  [here](https://github.com/Omniverse-Web3-Labs/omniverse-ink/blob/7e706564b7a5dc349829842197f6a18541b28f31/omniverse-protocol/lib.rs#L127).  

## Dev Example

### Data Structure

- As memtioned above, we need to create a data structure especially for Omniverse transactions, which is similiar to the native transaction data but more simple and could be verified by on-chain smart contract and similiar mechanisms (like `Substrate-Pallet`)

    ```rust
    #[derive(Debug, Decode, Encode, Clone)]
    #[cfg_attr(feature = "std", derive(scale_info::TypeInfo))]
    pub struct OmniverseTransactionData {
        pub nonce: u128,
        pub chain_id: u32,
        pub initiate_sc: Vec<u8>,
        pub from: [u8; 64],
        pub payload: Vec<u8>,
        pub signature: [u8; 65],
    }
    ```

- The `payload` in the above struct could be defined as follows:

    ```rust
    #[derive(Debug, Encode, Decode)]
    pub struct OmniverseFungible {
        pub op: u8,
        pub ex_data: Vec<u8>,
        pub amount: u128,
    }
    ```
    - For NFTs just substitute the `amount` to `id`

### Interfaces

- Operation Interfaces

    ```rust
    #[ink::trait_definition]
    pub trait FungibleToken {
        /// Sends an omniverse transaction
        #[ink(message)]
        fn send_omniverse_transaction(&mut self, data: OmniverseTransactionData) -> Result<(), Error>;
        /// Trigger execution
        #[ink(message)]
        fn trigger_execution(&mut self) -> Result<(), Error>;
        /// Set members
        #[ink(message)]
        fn set_members(&mut self, members: Vec<Member>) -> Result<(), Error>;
        /// Get members
        #[ink(message)]
        fn get_members(&self) -> Vec<Member>;
        /// Get executable transaction
        #[ink(message)]
        fn get_executable_delayed_transaction(&self) -> Option<([u8; 64], u128)>;
        /// Get omniverse balance
        #[ink(message)]
        fn balance_of(&self, pk: [u8; 64]) -> u128;
    }
    ```

    - the main interface is `send_omniverse_transaction`, which is especially for Omniverse Operations including `transfer`, `mint`, and `burn`

- The interfaces of state getters:

    ```rust
    #[ink::trait_definition]
    pub trait Omniverse {
        /// Get the number of omniverse transactions sent by user `pk`
        #[ink(message)]
        fn get_transaction_count(&self, pk: [u8; 64]) -> u128;
        /// Get the transaction data and timestamp of a user at a nonce
        #[ink(message)]
        fn get_transaction_data(&self, pk: [u8; 64], nonce: u128) -> Option<OmniverseTx>;
        /// Get the chain id
        #[ink(message)]
        fn get_chain_id(&self) -> u32;
        /// Get cached transaction
        #[ink(message)]
        fn get_cached_transaction(&self, pk: [u8; 64]) -> Option<OmniverseTx>;
        /// Set cooling down time
        #[ink(message)]
        fn set_cooling_down(&mut self, cd_time: u64) -> Result<(), Error>;
    }
    ```

### Implementation Example

We will just provide the implementation of the key interfaces here, and the whole picture could be found [here](https://github.com/Omniverse-Web3-Labs/omniverse-ink/tree/main/omniverse-protocol)  

- `send_omniverse_transaction`

    ```rust
    #[ink(message)]
    fn send_omniverse_transaction(&mut self, data: OmniverseTransactionData) -> Result<(), Error> {
        let member = self.members.get(&data.chain_id).ok_or(Error::NotMember)?;
        if member.contract_address != data.initiate_sc {
            return Err(Error::WrongInitiator);
        }

        let ret = self.send_omniverse_transaction_internal(data.clone());
        if ret == Ok(()) {
            self.delayed_txs.push((data.from, data.nonce));
        }
        ret
    }
    ```

    - Check if the transaction is initiated by a valid member (smart contract address on some allowed public chains)
    - Call `send_omniverse_transaction_internal`, which is as follows:  

    ```rust
    /// Verify an omniverse transaction
    fn send_omniverse_transaction_internal(&mut self, data: OmniverseTransactionData) -> Result<(), Error> {
        // Check if the sender is malicious
        let rc_ret = self.transaction_recorder.get(&data.from);
        if let Some(rc) = rc_ret {
            if rc.evil_tx_list.len() > 0 {
                return Err(Error::UserMalicious);
            }
        }

        // Verify the signature
        let ret = self.verify_transaction(&data);

        match ret {
            Ok(()) => {
                // Check cache
                let cache_ret = self.transaction_cache.get(&data.from);
                if cache_ret.is_some() {
                    return Err(Error::TransactionCached);
                }

                let cache = OmniverseTx::new(data.clone(), self.env().block_timestamp());
                // Logic verification
                self.check_execution(&data)?;
                self.transaction_cache.insert(data.from.clone(), cache);
                Self::env().emit_event(TransactionSent {
                    pk: data.from,
                    nonce: data.nonce,
                });
            }
            Err(Error::Duplicated) => {
                Self::env().emit_event(TransactionDuplicated {
                    pk: data.from,
                    nonce: data.nonce,
                });
            }
            Err(Error::Malicious) => {
                // Slash
            }
            _ => {

            }
        }

        ret
    }
    ```

    - The verification of the transaction data is the key point, including chacking the signature and whether there are double-spend-attack happens. The source code of the verification can be found [here](https://github.com/Omniverse-Web3-Labs/omniverse-ink/blob/7e706564b7a5dc349829842197f6a18541b28f31/omniverse-protocol/lib.rs#L301)  
        - The signature is happened off-chain with hash function `keccak256` and the `secp356k1` elliptic curve.  
        - The priciple of avoiding the double-spend-attack can be found [here](https://github.com/Omniverse-Web3-Labs/analysis-6358/blob/main/docs/dsa-analysis.md)
    - Then the transaction data will be added into the cache waiting for execution until the cooling time.
