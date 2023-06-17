# Test Guide for Milestone 2 from the W3F Grants

This document will show you how to deploy and test O-DLT.

The [system test tool](https://github.com/Omniverse-Web3-Labs/omniverse-system-test) is used to make the e2e test for the O-DLT, as well as deploy contracts on live networks.

## Prerequisites

- node >= v18
- npm >= 8.19
- git
- docker and docker-compose

## Installation

### Clone the repository

```
git clone -b milestone-2 --recursive https://github.com/Omniverse-Web3-Labs/omniverse-system-test.git
```

### Install

Enter the work directory, and execute the following commands
```
npm install
```

### Install for related projects

```
node src/index.js -i
```

## Run test

### Test of fungible tokens

#### Introduction

There are several steps in the full test flow  
- 1 Initialize the test
- 2 Run local nodes for all networks, currently EVM, INK and SUBSTRATE
- 3 Deploy contracts on all supported networks
- 4 Do some preparatory work for testing
- 5 Test
    - 5.1 Mint 100 token to `user1`
    - 5.2 `user1` transfer 11 token to `user2`
    - 5.3 Check the balance of `user2`
    - 5.4 Repeat 5.1 ~ 5.3 on all supported networks

#### Automatical test of fungible tokens
```
node src/index.js -t ft
```

The test will be over in about 5 minutes, and `Test competed and success` will be printed in the terminal if successful.

#### Test of fungible tokens with dockered synchronizer(Optional)
```
node src/index.js -t ft --docker
```

Everything is the same as the above test, except that you must launch the synchronizer yourself.

When the following message is printed in the terminal:
```
Have you launched the synchronizer(y)?
```

Open another terminal, enter the synchronizer directory, launch the synchronizer
```
cd submodules/omniverse-synchronizer
sudo ./docker/dockerize.sh test test --version
sudo bash ./docker/launch-synchronizer.sh
```

You can check the logs of the synchronizer
```
sudo docker logs -f test-test
```

Then continue the test by inputing 'y' and press 'Enter' in the terminal running test

### Test of swap

#### Introduction

There are several steps in the full test flow  
- 1 Initialize the test
- 2 Run local nodes for all networks, currently EVM, INK and SUBSTRATE
- 3 Deploy contracts on all supported networks
- 4 Do some preparatory work for testing
- 5 Test
    - 5.1 Mint 100 token to `user1`
    - 5.2 `user1` transfer 11 token to `user2`
    - 5.3 Check the balance of `user2`
    - 5.4 Repeat 5.1 ~ 5.3 on all supported networks

#### Automatical test of swap
```
node src/index.js -t swap
```

The test will be over in about 8 minutes, and `Test competed and success` will be printed in the terminal if successful.

#### Test of swap with dockered synchronizer(Optional)

```
node src/index.js -t swap --docker
```

The operating procedure is the same as shown in [Test of Fungible tokens with dockered synchronizer(Optional)](#test-of-fungible-tokens-with-dockered-synchronizer(Optional))

## Additional

If the test is executed successfully, the program will not be terminated `^C` is inputed, we can use tools for different networks to interact with the O-DLT.

The tools are
- EVM
```
cd submodules/omniverse-evm/contracts
node register/index.js -h // For help
```

- INK
```
cd submodules/omniverse-swap-tools/tool-for-ink
node index.js -h // For help
```

- SUBSTRATE
```
cd submodules/omniverse-swap-tools/omniverse-helper
node index.js -h // For help
```

**There is a [tutorial video]() showing the whole operation**