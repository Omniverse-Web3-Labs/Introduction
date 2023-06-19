# Auto-Tutorial

This guide descripes how to make omniverse operations with the omniverse CLI tools, and the code in the CLI tool is a basis for developers to build their `O-DLT` dApps.  

## Operations and Codes

Before commencing this tutorial, you should have generated a minimum of `O-DLT` token. To perform swap, you will need at least two `O-DLT` tokens. Now we have created two `O-DLT` tokens by [Auto-Deployment](./Auto-Deployment.md), the tokens id are `SKYWALKER` and `SKYWALKER1` respectively.

Command-line tool:

```sh
git clone git@github.com:Omniverse-Web3-Labs/omniverse-swap-tools.git
# before use
cd omniverse-helper
npm install
```

### Create Accounts

- You can create your omniverse accounts and get some gas tokens like [this](https://github.com/Omniverse-Web3-Labs/Omniverse-DLT-Introduction/tree/main/docs#omniverse-account).  

### Omniverse Transactions

All operations are performed within the `omniverse-helper` directory. Sender private key in file `.secret`.

- Mint
  - [Source Code](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools/blob/main/omniverse-helper/index.js#L181)
  - CLI: `node index.js -m CHAIN_NAME,TOKEN_ID,RECIPIENT,AMOUNT`.
  - Instruction: mint `AMOUNT` `TOKNE_ID` to `RECIPIENT`, the sender must be the owner of `TOKEN_ID`.
  - example:

    ```sh
    node index.js -m SUBSTRATE,SKYWALKER,0x8bb25caae0a466afde04833610cf0c998050693974188853bdb982ed60e5e08ee71b3c9c0f900f8191512787e47908277272f71f991cb15fa364bad8018ef40b,100
    ```

- Transfer
  - [Source Code](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools/blob/main/omniverse-helper/index.js#L164)
  - CLI: `node index.js -t CHAIN_NAME,TOKEN_ID,RECIPIENT,AMOUNT`.
  - Instruction: transfer `AMOUNT` `TOKNE_ID` to `RECIPIENT`.
  - example:

    ```sh
    node index.js -t SUBSTRATE,SKYWALKER,0x8bb25caae0a466afde04833610cf0c998050693974188853bdb982ed60e5e08ee71b3c9c0f900f8191512787e47908277272f71f991cb15fa364bad8018ef40b,100
    ```

- Balance
  - [Source Code](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools/blob/milestone-2/omniverse-helper/index.js#L222)
  - CLI: `node index.js -b CHAIN_NAME,TOKEN_ID,ACCOUNT`.
  - Instruction: query `ACCOUNT` balance of `TOKEN_ID`.
  - example:

    ```sh
    node index.js -m SUBSTRATE,SKYWALKER,0x8bb25caae0a466afde04833610cf0c998050693974188853bdb982ed60e5e08ee71b3c9c0f900f8191512787e47908277272f71f991cb15fa364bad8018ef40b
    ```

### Omniverse Token Swap

All operations are performed within the `omniverse-helper` directory.

- Users deposit to the swap platform
  - [Source Code](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools/blob/main/omniverse-helper/index.js#L314)
  - CLI: `node index.js -d CHAIN_NAME,TOKEN_ID,AMOUNT`.
  - Instruction: deposit `AMOUNT` `TOKEN_ID` into the swap.
  - example:

    ```sh
    node index.js -d SUBSTRATE,SKYWALKER,1001000
    ```

- Create Token Pool or add liquidity
  - [Source Code](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools/blob/main/omniverse-helper/index.js#L404)
  - CLI: `node index.js -al CHAIN_NAME,TRADING_PAIR_ID,TOKNE_X_ID,TOKEN_X_AMOUNT,TOKEN_Y_ID,TOKEN_Y_AMOUT`.
  - Instruction: if `TRADING_PAIR_ID` swap pool not exist than create, and add liquidity to `TRADING_PAIR_ID` swap pool.
  - example:
  
    ```sh
    node index.js -d SUBSTRATE,SKYWALKER/SKYWALKER1,SKYWALKER,1000000,SKYWALKER1,10000
    ```

    **note:** before create token pool, you need have deposited a sufficient amount of `TOKNE_X_ID` and `TOKNE_Y_ID`. For this example, we have deposited `1001000 SKYWALKER` and `10000 SKYWALKER1`.

- Users make swap from `TOKEN_X_ID` to `TOKEN_Y_ID`
  - [Source Code](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools/blob/milestone-2/omniverse-helper/index.js#L375)
  - CLI: `node index.js -x2y CHAIN_NAME,TRADING_PAIR_ID,AMOUNT`.
  - Instruction: swap `AMOUNT` `TOKEN_X` to get some `TOKEN_Y`.
  - example:
  
    ```sh
    node index.js -x2y SUBSTRATE,SKYWALKER/SKYWALKER1,1000
    ```

- Users make swap from `TOKEN_Y_ID` to `TOKEN_X_ID`
  - [Source Code](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools/blob/milestone-2/omniverse-helper/index.js#L444)
  - CLI: `node index.js -y2x CHAIN_NAME,TRADING_PAIR_ID,AMOUNT`.
  - Instruction: swap `AMOUNT` `TOKEN_Y` to get some `TOKEN_X`.
  - example:
  
    ```sh
    node index.js -y2x SUBSTRATE,SKYWALKER/SKYWALKER1,10
    ```

- Users withdraw from the swap platform
  - [Source Code](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools/blob/milestone-2/omniverse-helper/index.js#L337)
  - CLI: `node index.js -w CHAIN_NAME,TOKEN,AMOUNT`
  - Instruction: withdraw `AMOUNT` `TOKEN_ID` from swap.
  - example:
  
    ```sh
    node index.js -w SUBSTRATE,SKYWALKER,10
    ```

- Users query the `TOKEN_ID` deposited balance in the swap
  - [Source Code](https://github.com/Omniverse-Web3-Labs/omniverse-swap-tools/blob/milestone-2/omniverse-helper/index.js#L243)
  - CLI: `node index.js -bs CHAIN_NAME,TOKEN_ID,ACCOUNT`
  - Instruction: query the `TOKEN_ID` deposited balance in the swap of `ACCOUNT`
  - example:

    ```sh
    node index.js -bs SUBSTRATE,SKYWALKER,0x8bb25caae0a466afde04833610cf0c998050693974188853bdb982ed60e5e08ee71b3c9c0f900f8191512787e47908277272f71f991cb15fa364bad8018ef40b
    ```
