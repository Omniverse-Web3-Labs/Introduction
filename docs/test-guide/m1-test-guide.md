# Test Guide for Milestone 1 from the W3F Grants

The details of how to use the `O-DLT` protocol have already existed, and this test guide will provide a step by step testing guide based on the [previous tutorial](../README.md).  

## Testing Steps

### Deployment

For simplicity, we highly recommend you to use our [pre-deployed environment](../README.md#environment) to test the functions.  

If you want to make the deployment from the first beginning, although it is not included in this milestone, we still provided a complete [deployment tutorial](../Deployment.md) to guide users to prepare their own environment step by step. We suppose you are familiar with the basic pieces of knowledge of both [Substrate](https://substrate.io/) and [Ethereum](https://ethereum.org/en/) along with their tools.  

- [Deploy the `O-DLT` example Parachain](../Deployment.md#substrate)
- [Deploy the `O-DLT` example EVM Smart Contracts](../Deployment.md#evm-compatible-chain)  
- [Deploy the off-chain Synchronizers](../Deployment.md#synchronizer)  

### Tools Installation

Actually, there's no need for new kind of wallets to operate `O-DLT` tokens, and what we need is just integrating existing wallets when developing front Dapps.  
As the time is limited, now we have just provided a CLI Client to operate `O-DLT` Tokens.  

- [Install CLI Tools](../README.md#tools-install)  

### Test Operations

#### Create Omniverse Account

An omniverse account can be directly used for both Polkadot and EVM chains. 
