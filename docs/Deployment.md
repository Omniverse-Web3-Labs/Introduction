# Tutorial for deploying an Omniverse Token

From this tutorial, you can learn how to deploy an Omniverse FT or NFT, which almost follow the same process. 

In this tutorial, you will use these repos:
- [omniverse-evm](https://github.com/Omniverse-Web3-Labs/omniverse-evm): Contains the contract code for EVM-compatible chains.  
- [omniverse-swap](https://github.com/Omniverse-Web3-Labs/omniverse-swap): Contains the pallets for substrate.  
- [omniverse-synchronizer](https://github.com/Omniverse-Web3-Labs/omniverse-synchronizer): The synchronizer responsible for synchronizing messages between chains.
- [omniverse-swap-tools](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools): The tool to interact with substrate.

## Prerequisites
- Truffle >= v5.7.9
- Ganache >= v7.7.5(If you want to run tests for `omniverse-evm`)
- Node >= v18.12.1
- NPM >= 8.19.2
- Metamask
- Remix
- git
- build-essential
- make
- llvm
- clang
- curl
- libssl-dev
- protobuf-compiler

## Deployment

### Substrate

You can start a local Omniverse-DLT substrate node.

#### To compile the Omniverse-DLT Substrate node

  1. Open a terminal shell on your computer.
  
  2. Clone the node repository by running the following command:

  ```bash
  git clone https://github.com/Omniverse-Web3-Labs/omniverse-swap.git
  ```

  3. Change to the root of the node template directory by running the following command:

  ```bash
  cd omniverse-swap
  ```

  4. Compile the node template by running the following command:

  ```bash
  cargo build --release  
  ```

#### start the local Omniverse-DLT node

After your node compiles, you are ready to start exploring what it does using [polkadot-js](https://polkadot.js.org/) or [contract_ui](https://contracts-ui.substrate.io/).

  1. Open a terminal shell.

  2. Change to the root directory where you compiled the Omniverse-DLT node.

  3. Start the node in development mode by running the following command:

  ```bash
  ./target/release/node-template  --dev
  ```

  4. Verify your node is up and running successfully by reviewing the output displayed in the terminal. The terminal should display output similar to this:

  ```bash
  2023-03-08 23:11:50 Substrate Node
  2023-03-08 23:11:50 ✌️  version 4.0.0-dev-f6dccb957e1
  2023-03-08 23:11:50 ❤️  by Substrate DevHub <https://github.com/substrate-developer-hub>, 2017-2023
  2023-03-08 23:11:50 📋 Chain specification: Development
  2023-03-08 23:11:50 🏷  Node name: jittery-bridge-0008
  2023-03-08 23:11:50 👤 Role: AUTHORITY
  2023-03-08 23:11:50 💾 Database: RocksDb at ./data/chains/dev/db/full
  2023-03-08 23:11:50 ⛓  Native runtime: node-template-100 (node-template-1.tx1.au1)
  2023-03-08 23:11:51 Using default protocol ID "sup" because none is configured in the chain specs
  2023-03-08 23:11:51 🏷  Local node identity is: 12D3KooWDUSyMnSB3Nb3XMe2vqdd89sKWTNx5BWMWi82gCGcy1tA
  ...
  ...
  ...
  ...
  2023-03-08 23:11:56 💤 Idle (0 peers), best: #21499 (0x2b15…a013), finalized #21497 (0xb0aa…0edb), ⬇ 0 ⬆ 0
  ```
  If the number after `finalized` is increasing, your blockchain is producing new blocks and reaching consensus about the state they describe.
  
  5. Keep the terminal that displays the node output open to continue.


### EVM-compatible chain
You can deploy the contracts on any EVM-compatible chain, but let us use Goerli as example. Here I assume you are familiar with Ethereum, at least knowing how to crate an account and receiving some tokens from faucet.

#### Clone omniverse-evm
Enter your work directory, let's say `<WORK_DIR>`, input the code
```
git clone git@github.com:Omniverse-Web3-Labs/omniverse-evm.git
```

#### Launch Remix
- Open the website
Open the Chrome, navigate to the address `https://remix.ethereum.org`

- Install Remixd
Remixd is an NPM module that intends to be used with Remix IDE web and desktop applications. It establishes a two-way websocket connection between the local computer and Remix IDE for a particular project directory.

```
npm install -g @remix-project/remixd
```

- Start remixd
```
remixd -s <WORK_DIR>
```
`<WORK_DIR>` is specified above

- Open workspace
Click `-connect to localhost-` on Remix.
[image here]

Then click `Connect` in the popup window.
[image here]

#### Deploy contracts
- Open files
Open files `SkywalkerFungible` and `libraries/OmniverseProtocolHelper.sol`.
[image here]

- Compile files
Compile the files respectively by choosing one file and clicking the `compile` button
[image here]

- Deploy `SkywalkerFungible`
Choose the network as `Georli` and switch the account with witch you will deploy the contract.
[image here]

Enter the `Deploy` page
[image here]

Choose the environment as `Injected Provider - MetaMask` and choose the contract as `SkywalkerFungible`
[image here]

You must input the chain id, which indicates on which chain the contract will be deployed, token name and token symbol. Then click the `Deploy` button, you will be asked to sign two transactions later, the first is for `OmniverseProtocolHelper`, the latter is for `SkywalkerFungible`.
[image here]

### Synchronizer
#### Clone `omniverse-synchronizer`
```
git clone git@github.com:Omniverse-Web3-Labs/omniverse-synchronizer.git
```

#### Install
```
cd omniverse-synchronizer
npm install
```

#### Change configuration
Open the file `config/default.json`
- Set the address of the contract `SkywalkerFungible` deployed above to the field `GOERLI`.`skywalkerFungibleContractAddress`.
- Set the node address of your substrate node to the field `SUBSTRATE`.`nodeAddress`.
[image here]

## Initialization
### `Substrate`


### `Omniverse-evm`
#### Set cooling down time
The cooling down time is used to limit the speed of an omniverse transaction, in order that there is enough time to deal with conflicts.

Call the method `setCoolingDownTime` of `SkywalkerFungible` in Remix, with argument `10`, which means the cooling down time is 10s.

[image here]

#### Set members
The members determine which chains are supported by the omniverse token.

Call the method `setMembers` of `SkywalkerFungible` in Remix, with argument `[[2, <EVM-CONTRACT-ADDRESS>], [1, SUBSTRATE-TOKEN-ID>]]`, which means there are two members, one is the chain with id `2` and contract `<EVM-CONTRACT-ADDRESS>`, the other one is the chain with id `1` and token id `SUBSTRATE-TOKEN-ID`.

[image here]

### `Substrate`
#### Set metadata

#### Start
```
npm src/main.js
```

You can see outputs on the screen like this
[image here]



#### Set base URI
The base URI is used to index the metadata of 