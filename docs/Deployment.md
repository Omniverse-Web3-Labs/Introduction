# Tutorial for deploying an Omniverse Token

From this tutorial, you can learn how to deploy an Omniverse FT or NFT, which almost follow the same process. 

In this tutorial, you will use these repos:
- [omniverse-evm](https://github.com/Omniverse-Web3-Labs/omniverse-evm): Contains the contract code for EVM-compatible chains.  
- [omniverse-swap](https://github.com/Omniverse-Web3-Labs/omniverse-swap): Contains the pallets for substrate.  
- [omniverse-synchronizer](https://github.com/Omniverse-Web3-Labs/omniverse-synchronizer): The synchronizer responsible for synchronizing messages between chains.
- [omniverse-swap-tools](git@github.com:Omniverse-Web3-Labs/omniverse-swap-tools.git): The tool to interact with substrate.

## Prerequisites
- Truffle >= v5.7.9
- Ganache >= v7.7.5(If you want to run tests for `omniverse-evm`)
- Node >= v18.12.1
- NPM >= 8.19.2
- Metamask
- Remix
- git
[Prerequisites for substrate]

## Deployment
### Substrate

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

Then click `Connect` in the popup window.

#### Deploy contracts
- Open files `SkywalkerFungible` and `libraries/OmniverseProtocolHelper.sol`.
[image here]

- Compile the files respectively by choosing one file and clicking the `compile` button
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

### Substrate
[Add it here]

### Synchronizer
#### Clone `omniverse-synchronizer`

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
### `Omniverse-evm`


#### Start
```
npm src/main.js
```

You can see outputs on the screen like this
[image here]