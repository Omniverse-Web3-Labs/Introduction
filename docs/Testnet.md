# Testnet information for development

## Miracle

### Networks

- SUBSTRATE
    - RPC: http://3.122.90.113:9911
    - WS: `ws://3.122.90.113:9922`
    - Explorer: [https://polkadot.js.org/apps](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F3.122.90.113%3A9922#/chainstate)
    - SkywalkerFungible: `SKYWALKER` in `assets` pallet
    - SkywalkerNonFungible: `SKYWALKERNFT` in `uniques` pallet
    - Omniverse chain id: 1
- SEPOLIA
    - RPC: https://sepolia.infura.io/v3/94ebec44ffc34501898dd5dccf387f81
    - Explorer: [https://sepolia.etherscan.io/](https://sepolia.etherscan.io/)
    - SkywalkerFungible: 0x64aEcC149f292eCbCf8Dd93B320d5a9780aba191
    - SkywalkerNonFungible: 0x081Ba0C5C458F1D350F95dc4e6Dc172e69F8Fff7
    - Omniverse chain id: 5
- BSCTEST
    - RPC: https://bsc-testnet.public.blastapi.io
    - Explorer: [https://testnet.bscscan.com/](https://testnet.bscscan.com/)
    - SkywalkerFungible: 0x12B22989407C8E6C69df5477AbD7b569b024Aba0
    - SkywalkerNonFungible: 0x1AF65Fa4fd838074980CadB398969C9fA10c9Ce7
    - Omniverse chain id: 0
- MUMBAI
    - RPC: https://rpc-mumbai.maticvigil.com
    - Explorer: [https://mumbai.polygonscan.com/](https://mumbai.polygonscan.com/)
    - SkywalkerFungible: 0x1181e9bbb48a5448c81cf1a2532a3d4257c69e22
    - SkywalkerNonFungible: 0x4F77711365BB96969D763Fc8CB6cB40964aC94Ce
    - Omniverse chain id: 6
- PLATON
    - RPC: https://openapi2.platon.network/rpc
    - Explorer: [https://scan.platon.network/](https://scan.platon.network/)
    - SkywalkerFungible: 0x0791B79Ba0DC124dd357633Bf298719aa12f7D59
    - SkywalkerNonFungible: 0x13A689B55FF8Bf86a8dEAC357553eabDd93f78fb
    - Omniverse chain id: 4

### Interfaces

EVM: Refer to [https://github.com/Omniverse-Web3-Labs/omniverse-evm/tree/miracle-show](https://github.com/Omniverse-Web3-Labs/omniverse-evm/tree/miracle-show)

### Update

#### 2023/4/7

- Support EIP191: Able to verify the signature signed by MetaMask
- Remove Goerli and MoonbaseAlpha
- Add Sepolia and Mumbai