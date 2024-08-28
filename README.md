# Introduction

## Index

- [Ecosystems Supported](#ecosystems-supported)
- [Proposals](#proposals)
- [Grants](#grants)

## Overview

<p align='center' id='architecture'>Figure.1 Architecture</p> 

![image](https://github.com/Omniverse-Web3-Labs/Omniverse-DLT-Introduction/assets/83746881/352a2c16-baa0-4552-909c-372d1e7b6eed)

The Omniverse DLT is a new **application-level** (ERC) token protocol built upon multiple existing L1 public chains, enabling asset-related operations such as transfers and receptions running **globally** and **synchronously** over different consensus spaces.

## Motivation

- The current paradigm of token bridges makes assets fragment.  
- If tokens like ETH, DOT were transferred to another chain through the current token bridge, if the chain broke down, it will be lost for users.  

The core of `O-DLT` is **synchronization** instead of **bridge-transferring**, even if all the other chains break down, as long as one chain is still running, user’s assets will not be lost. 

- The fragment problem will be solved.
- The security of users' multi-chain assets can be greatly enhanced.

## Rationale

With `O-DLT`, we can create a global token protocol, that leverages smart contracts or similar mechanisms on existing blockchains to record the token states synchronously. The synchronization could be made by trustless off-chain synchronizers.  

### Architecture

As shown in [Figure.1](#overview), smart contracts deployed on multi-chains execute `o-transactions` of `O-DLT` tokens synchronously through the trustless off-chain synchronizers.   

- The `O-DLT` smart contracts are referred to as **Abstract Nodes**. The states recorded by the Abstract Nodes that are deployed on different blockchains respectively could be considered as copies of the global state, and they are ultimately consistent.  
- **Synchronizer** is an off-chain execution program responsible for carrying published  `o-transactions` from the `O-DLT` smart contracts on one blockchain to the others. The synchronizers work trustless as they just deliver `o-transactions` with others' signatures, and details could be found in the [workflow](#workflow).

### Principle

- There should be a global user identifier for O-DLT, which is recommended to be referred to as Omniverse Account (`o-account` for short) in this article. The `o-account` is recommended to be expressed as a public key created by the elliptic curve `secp256k1`. A [mapping mechanism](#mapping-mechanism-for-different-environments) is recommended for different environments.  
- The synchronization of the `o-transactions` guarantees the ultimate consistency of token states across all chains. The related data structure can be found [here: `solidity`](https://github.com/Omniverse-Web3-Labs/omniverse-evm/blob/7d9129fc15cef4b9e23c27fc3e0e220e88bffcc8/contracts/contracts/interfaces/IERC6358.sol#L13) and [here: `rust`](https://github.com/Omniverse-Web3-Labs/omniverse-swap/blob/5e50159da24cdfa867cdc5b69427cc49aed83c4d/pallets/omni-protocol/src/types.rs#L81).

    - A `nonce` mechanism is brought in to make the states consistent globally.
    - The `nonce` appears in two places, the one is `nonce in o-transaction` data structure, and the other is `account nonce` maintained by on-chain `O-DLT` smart contracts. 
    - When synchronizing, the `nonce in o-transaction` data will be checked by comparing it to the `account nonce`.

#### Workflow

- Suppose a common user `A` and her related operation `account nonce` is $k$.
- `A` initiates an `o-transaction` on Ethereum by calling `IERC6358::sendOmniverseTransaction`. The current `account nonce` of `A` in the `O-DLT` smart contracts deployed on Ethereum is $k$ so the valid value of `nonce in o-transaction` needs to be $k+1$.  
- The `O-DLT` smart contracts on Ethereum verify the signature of the `o-transaction` data. If the verification succeeds, the `o-transaction` data will be published by the smart contracts on the Ethereum side. The verification includes:
    - whether the balance (FT) or the ownership (NFT) is valid
    - and whether the `nonce in o-transaction` is $k+1$
- The `o-transaction` SHOULD NOT be executed on Ethereum immediately, but wait for a time.  
- Now, `A`'s latest submitted `nonce in o-transaction` on Ethereum is $k+1$, but still $k$ on other chains.
- The off-chain synchronizers will find a newly published `o-transaction` on Ethereum but not on other chains.  
- Next synchronizers will rush to deliver this message because of a rewarding mechanism. (The strategy of the reward could be determined by the deployers of `O-DLT` tokens. For example, the reward could come from the service fee or a mining mechanism.) 
- Finally, the `O-DLT` smart contracts deployed on other chains will all receive the `o-transaction` data, verify the signature and execute it when the **waiting time is up**. 
- After execution, the `account nonce` on all chains will add 1. Now all the `account nonce` of account `A` will be $k+1$, and the state of the balances of the related account will be the same too.  

## Reference Implementation

### Omniverse Account

- An Omniverse Account example: `3092860212ceb90a13e4a288e444b685ae86c63232bcb50a064cb3d25aa2c88a24cd710ea2d553a20b4f2f18d2706b8cc5a9d4ae4a50d475980c2ba83414a796`
    - The Omniverse Account is a public key of the elliptic curve `secp256k1`
    - The related private key of the example is:  `cdfa0e50d672eb73bc5de00cc0799c70f15c5be6b6fca4a1c82c35c7471125b6`

#### Mapping Mechanism for Different Environments

In the simplest implementation, we can just build two mappings to get it. One is like `pk based on sece256k1 => account address in the special environment`, and the other is the reverse mapping. 

The `Account System` on `Flow` is a typical example.  

- `Flow` has a built-in mechanism for `account address => pk`. The public key can be bound to an account (a special built-in data structure) and the public key can be got from the `account address` directly.  
- A mapping from `pk` to the `account address` on Flow can be built by creating a mapping `{String: Address}`, in which `String` denotes the data type to express the public key and the `Address` is the data type of the `account address` on Flow.  

### Ecosystems Supported

- [Ethereum (EVMs)](https://github.com/Omniverse-Web3-Labs/omniverse-evm)
- `Bitcoin`: the ERC-6358 style token is implemented by the `Ordinals`, in details of which is derived from `BRC-20`/`BRC-721` protocol. The example of ERC-6358 protocol on `BTC` can be found at the [Ordinals Explorer of the `BTC` testnet](https://testnet.ordinals.com/inscription/22267e023cf23ebe0e7275d662743e884bfa5c33276eb5e642539208f9362294i0)  
- Polkadot
  - [Pallet](https://github.com/Omniverse-Web3-Labs/omniverse-swap)
  - [`Ink!`](https://github.com/Omniverse-Web3-Labs/omniverse-ink)
- [Flow](https://github.com/Omniverse-Web3-Labs/omniverse-flow)

## Security Considerations

### Attack Vector Analysis

According to the above, there are two roles:

- **common users** are who initiate an `o-transaction`
- **synchronizers** are who just carry the `o-transaction` data if they find differences between different chains.  

The two roles might be where the attack happens:  

#### **Will the *synchronizers* cheat?**  

- Simply speaking, it's none of the **synchronizer**'s business as **they cannot create other users' signatures** unless some **common users** tell him, but at this point, we think it's a problem with the role **common user**.  
- The **synchronizer** has no will and cannot do evil because the `o-transaction` data that they deliver is verified by the related **signature** of other **common users**.  
- The **synchronizers** would be rewarded as long as they submit valid `o-transaction` data, and *valid* only means that the signature and the amount are both valid. This will be detailed and explained later when analyzing the role of **common user**.  
- The **synchronizers** will do the delivery once they find differences between different chains:
    - If the current `account nonce` on one chain is smaller than a published `nonce in o-transaction` on another chain
    - If the transaction data related to a specific `nonce in o-transaction` on one chain is different from another published `o-transaction` data with the same `nonce in o-transaction` on another chain

- **Conclusion: The *synchronizers* won't cheat because there are no benefits and no way for them to do so.**

#### **Will the *common user* cheat?**

- Simply speaking, **maybe they will**, but fortunately, **they can't succeed**.    
- Suppose the current `account nonce` of a **common user** `A` is $k$ on all chains. `A` has 100 token `X`, which is an instance of the `O-DLT` token.    
- Common user `A` initiates an `o-transaction` on a Parachain of Polkadot first, in which `A` transfers `10` `X`s to an `o-account` of a **common user** `B`. The `nonce in o-transaction` needs to be $k+1$. After signature and data verification, the `o-transaction` data(`ot-P-ab` for short) will be published on Polkadot.
- At the same time, `A` initiates an `o-transaction` with the **same nonce** $k+1$ but **different data**(transfer `10` `X`s to another `o-account` `C` for example) on Ethereum. This `o-transaction` (named `ot-E-ac` for short) will pass the verification on Ethereum first, and be published.  
- At this point, it seems `A` finished a ***double spend attack*** and the states on Polkadot and Ethereum are different.  
- **Response strategy**:
    - As we mentioned above, the synchronizers will deliver `ot-P-ab` to Ethereum and deliver `ot-E-ac` to Polkadot because they are different although with the same nonce. The synchronizer who submits the `o-transaction` first will be rewarded as the signature is valid.
    - Both the `O-DLT` smart contracts or similar mechanisms on Polkadot and Ethereum will find that `A` did cheating after they received both `ot-E-ac` and `ot-P-ab` respectively as the signature of `A` is non-deniable.  
    - We have mentioned that the execution of an `o-transaction` will not be done immediately and instead there needs to be a fixed waiting time. So the `double spend attack` caused by `A` won't succeed.
    - There will be many synchronizers waiting for delivering o-transactions to get rewards. So although it's almost impossible that a **common user** can submit two `o-transactions` to two chains, but none of the synchronizers deliver the `o-transactions` successfully because of a network problem or something else, we still provide a solution:  
        - The synchronizers will connect to several native nodes of every public chain to avoid the malicious native nodes.
        - If it indeed happened that all synchronizers' network break, the `o-transactions` will be synchronized when the network recovered. If the waiting time is up and the cheating `o-transaction` has been executed, we are still able to revert it from where the cheating happens according to the `nonce in o-transaction` and `account nonce`.
- `A` couldn't escape punishment in the end (For example, lock his account or something else, and this is about the certain tokenomics determined by developers according to their own situation).  

- **Conclusion: The *common user* maybe cheat but won't succeed.**

## Index

- [Pitch Deck](./Deck.pdf)
- [Tutorial](./docs/README.md)
- [Principle of Omniverse AMM](./docs/Principle%20of%20Omniverse%20AMM.md)

## Demo

- [Demo Video](https://o20k.s3.us-west-2.amazonaws.com/omniverse-swap.mp4)
- [Demo for BTC based on zk and ordinals](https://omniversedlt.s3.amazonaws.com/omniverse/zk-6358-ordinals.mp4)

## Proposals

- [ERC-6358](https://eips.ethereum.org/EIPS/eip-6358)
- [BTC-Proposal](https://github.com/Omniverse-Web3-Labs/bitcoin-proposals)

## Grants

- Web3 Foundation

<p align='center' id='w3f'>Figure.2 Web3 Foundation</p>

![image](https://user-images.githubusercontent.com/83746881/232069565-2de7a85c-fea5-4246-b194-ab3e4e3ddf60.png)

