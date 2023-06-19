# Auto-Deployment

## Prerequisites

- Ubuntu 20.04
- docker and docker-compose
- node >= v18.12
- npm >= 8.19

## Install and start the Parachains and EVM chain locally for `O-DLT` token

We have made Docker images for these nodes, so you can launch nodes easily
```
wget xxxxxx
docker-compose up -d
```

The following chains will be installed and launched locally:
    - Ink! Parachain
    - Swap Parachain
    - Local EVM chain

If you do not change ports or other fields in the `docker-compose.yaml`, you can get node addresses shown below:

- Local EVM chain: http://127.0.0.1:10100
- Ink! Parachain: ws://127.0.0.1:10101
- Swap Parachain: ws://127.0.0.1:10102

## Auto-deploy `O-DLT` token and initialization

We use the system tool which is shown in [Test guide](https://github.com/Omniverse-Web3-Labs/Omniverse-DLT-Introduction/blob/main/docs/test-guide/m2-test-guide.md) to deploy contracts

### Configure

Replace `config/default.json` with `config/deploy.template.json`
```
cp config/deploy.template.json config/default.json
```

Open `config/default.json`

`tokenInfo` is the token information of the omniverse tokens you will deploy, you can change it as what you like

```
"tokenInfo": {
        "ft": [{
            "name": "SKYWALKER",
            "symbol": "SKYWALKER"
        }]
    },
```

`networks` contains the chains on which you will deploy omniverse tokens

- rpc: `http` end point of a node connected to the network
- ws: `websocket` end point of a node connected to the network
- chainType: What kind of chain you will deploy omniverse token on, currently you can choose: EVM, INK, SUBSTRATE
- omniverseChainId: The omniverse chain id is set for all public chains, namely each chain will have a unique omniverse chain id. Currently, you can set any id the this field, just keep it unique in the configuration.
- coolingDown: Cooling down time, the interval between two omniverse transactions, just let what it is if you use local nodes.
- chainId: The field is the EVM chain id, so it is only used when the chain type is `EVM`.

We have configured this file for this demonstration, so you do not need to change it.

### Auto-deployment and initializations

```
node src/index.js -d ft
```

This process is almost the same as [test guide](https://github.com/Omniverse-Web3-Labs/Omniverse-DLT-Introduction/blob/main/docs/test-guide/m2-test-guide.md#explaination-of-fungible-tokens-test), except that it will not run test cases.
    
The following things will be done in the deployment process
    - Deployment of the related (set in the `default.json`) Ink! ERC-20 
    - Deployment of the related (set in the `default.json`) EVM ERC-20 
    - Deployment of the related (set in the `default.json`) Pallet ERC-20
    - Deployment of Pallet Swap
    - members, cooling time, decimal
    - gas tokens for accounts

## Launch the auto-synchronizer

You can use the `omniverse-synchronizer` in `./submodules`

### Configure

The config file `config/default.json` and secret key file `.secret` will be created automatically after you run deploy command. You do not need to change it here, you can refer the [Omniverse-synchronizer](https://github.com/Omniverse-Web3-Labs/omniverse-synchronizer/blob/milestone-2/README.md) for more information.

### Launch the synchronizer

Enter the synchronizer directory, build a docker image for synchronizer
```
cd submodules/omniverse-synchronizer
sudo ./docker/dockerize.sh test test --version
```

The docker image `test-test:1.0.0` will be built, the version `1.0.0` is derived from the version in the `package.json`.

You can change the image name, and push to a docker image repository for future use. We do not need to do that in this tutorial here.

Execute the following command to launch a synchronizer.
```
sudo bash ./docker/launch-synchronizer.sh
```

You can check the logs of the synchronizer
```
sudo docker logs -f test-test
```

## Note(Option) 

- Launch more auto-synchronizers

    If you want to start more auto-synchronizers, repeat the [Launch the auto-synchronizer](#launch-the-auto-synchronizer)

- Create more omniverse tokens

    If you want to create more omniverse tokens, repeat the [Auto-deploy `O-DLT` token and initillization](#auto-deploy-o-dlt-token-and-initillization) and [Launch the aotu-synchronizer](#launch-the-aotu-synchronizer)

- Deploy on public EVM chains


