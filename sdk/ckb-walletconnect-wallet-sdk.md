# Introduction
This article will present the usage of `CKB's WalletConnect Wallet SDK`, an auxiliary library built on top of WalletConnect which wraps the protocol for integration within the CKB ecosystem.

## Why do I need WalletConnect in my wallet?

WalletConnect is an established chain-agnostic open source protocol for connecting decentralized applications to wallets. Whereas there are different options on how to safely implement such connection, WalletConnect is a widely supported standard across different wallets, chains and applications, and it's technical approach is simple, safe and proven. 

WalletConnect provides a more secure and convenient experience for users and reduces the difficulty of wallet integration for dapp developers. More and more dapps and three-way wallet integration libraries are authorising and signing transactions via WalletConnect. 


## Requirements
Create an account on Wallet Connect website and then create a new Project, it's super easy, with just a few fields on the form. After that, you will be able to get your projectId and use it on your wallet.

## Installation
Install the dependency on your client-side application

```
npm i @nervosnetwork/ckb-walletconnect-wallet-sdk
```

## Setup
Initialize the SDK with the following code:
```
import CkbWallet from '@nervosnetwork/ckb-walletconnect-wallet-sdk'

const ckbWallet = new CkbWallet({
  clientOptions: {
    projectId: '<project id>', // the ID of your project on Wallet Connect website
    metadata: {
      name: 'MyWalletnName',
      description: 'My Wallet description', 
      url: 'https://mywalletdescription.app/',
      icons: ['https://mywalletdescription.app/mywalleticon.png'],
    },
  },
  method: {
    ckb_getAddresses: <getAddresses function>,
    ckb_signTransaction: <signTransaction function>,
    ckb_signMessage: <signMessage function>,
  },
})

await ckbWallet.init()
```

## Using the SDK

### Connect to the Dapp
Start the process of establishing a new connection. It will create a new proposal that needs to be accepted or rejected.

```
await ckbWallet.connect(
  'wc:1d47f6e0-b21c-4c06-8cfe-142926fa1740@1?bridge=https%3A%2F%2F0.bridge.walletconnect.org&key=55433f7778ae7437fcc0579828c770b0517f592b81a76b4c289d09eb964d7091'
)
```

### Disconnect from a specific dapp session
```
const session = ckbWallet.sessions[0]
await ckbWallet.disconnect(session)
```

### Approve a specific connection proposal
```
const proposal = ckbWallet.proposals[0]
await ckbWallet.approveProposal(proposal, {
  account: "ckb:testnet:ckt1qzda0cr08m85hc8jlnfp3zer7xulejywt49kt2rr0vthywaa50xwsqwh8z6fkne05j0emqeen59qnn8a6xkm3fs0xf9en"
})
```

### Reject a specific connection proposal
```
const proposal = ckbWallet.proposals[0]
await ckbWallet.rejectProposal(proposal)
```

### Approve a specific session request
```
const request = ckbWallet.requests[0]
await ckbWallet.approveRequest(request)
```

### Reject a specific session request
```
const request = ckbWallet.requests[0]
await ckbWallet.rejectRequest(request)
```

### Emit a event to a specific dapp session
```
const session = ckbWallet.sessions[0]
await ckbWallet.emitEvent(session, {
  name: 'chainChanged',
  data: ['ckb:testnet']
})
```