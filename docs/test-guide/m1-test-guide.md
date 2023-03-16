# Test Guide for Milestone 1 from the W3F Grants

The details of how to use the `O-DLT` protocol have already existed, and this test guide will provide a step by step testing guide based on the [previous tutorial](../README.md).  

## **Testing Steps**

### **Deployment**

**For simplicity, we highly recommend you to use our [pre-deployed environment](../README.md#environment) to test the functions.**  

If you want to make the deployment from the first beginning, although it is not included in this milestone, we still provided a complete [deployment tutorial](../Deployment.md) to guide users to prepare their own environment step by step. We suppose you are familiar with the basic pieces of knowledge of both [Substrate](https://substrate.io/) and [Ethereum](https://ethereum.org/en/) along with their tools.  

- [Deploy the `O-DLT` example Parachain](../Deployment.md#substrate)
- [Create your own `O-DLT` token on the Parachain](../README.md#create-your-own-omniverse-token)
- [Deploy the `O-DLT` example EVM Smart Contracts](../Deployment.md#evm-compatible-chain)  
- [Deploy the off-chain Synchronizers](../Deployment.md#synchronizer)  
- [Initialization](../Deployment.md#initialization)  
    - [`O-DLT` token on Parachain](../Deployment.md#substrate-1)
    - [`O-DLT` token on EVM](../Deployment.md#evm-compatible-chain-1)

### **Tools Installation**

Actually, there's no need for new kind of wallets to operate `O-DLT` tokens, and what we need is just integrating existing wallets when developing front Dapps.  
As the time is limited, now we have just provided a CLI Client to operate `O-DLT` Tokens.  

- [Install CLI Tools](../README.md#tools-install)  

### **Test Operations**

#### **Create Omniverse Account**

Temporarily, An omniverse account is a public key generated with the elliptic curve `secp256k1`, which can be used for both Polkadot and EVM chains.  

- We have provided a simple way to [generate and check](../README.md#omniverse-account) the omniverse account based on `Polkadot.js/apps` and `CLI Tools`  

#### **Transactions of the `O-DLT` tokens**

We have provided detailed guidance on the commonly used omniverse operations, including `Claim`, `Check balance`, and `transferring`, for both the fungible tokens and the non-fungible tokens.  
To directly test the operations (*highly recommended for efficiency*), you can try the `skywalker` fungible token and the `skywal` NFT already deployed.   

- [For Fungible Tokens](../README.md#omniverse-fungible-token)  
- [For Non-Fungible Tokens](../README.md#omniverse-non-fungible-token)

To test the operations of your own token, make the [deployment and initialization](#deployment) first.  
