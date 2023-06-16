# Test Guide for Milestone 2 from the W3F Grants

This document will show you how to deploy and test O-DLT.  

## Test with `system test tool`

The [system test tool]() is used to make the e2e test for the O-DLT, as well as deploy contracts on live networks.

### Prerequisites

- node >= v18
- npm >= 8.19
- git

### Install

#### Clone the repository

```
git clone -b feature-ink-nodb --recursive https://github.com/Omniverse-Web3-Labs/omniverse-system-test.git
```

#### Install

Enter the work directory, and execute the following commands
```
npm install
```

#### Install for related projects

```
node src/index.js -i
```

### Run test

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

#### Automatical test
```
node src/index.js -t
```

The test will be over in about 3 minutes, and `Success` will be printed in the terminal if successful.

#### Manual test(Optional)

If the test is executed successfully, the program will not be terminated, we can use tools for different networks to interact with the O-DLT.

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