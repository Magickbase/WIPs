# Introduction
This article will present the usage of `CKB's WalletConnect Wallet SDK`, an auxiliary library built on top of WalletConnect which wraps the protocol for integration within the CKB ecosystem.

## Why do I need WalletConnect in my wallet?

WalletConnect is an established chain-agnostic open source protocol for connecting decentralized applications to wallets. Whereas there are different options on how to safely implement such connection, WalletConnect is a widely supported standard across different wallets, chains and applications, and it's technical approach is simple, safe and proven. 

WalletConnect provides a more secure and convenient experience for users and reduces the difficulty of wallet integration for dapp developers. More and more dapps and three-way wallet integration libraries are authorising and signing transactions via WalletConnect. 


## Requirements
Create an account on Wallet Connect website and then create a new Project, it's super easy, with just a few fields on the form. After that, you will be able to get your projectId and use it on your wallet.

## Getting Started

### Install

```
npm install ckb-walletconnect-wallet-sdk
```

### Usage

- Initialization

```javascript
import { Core } from "@walletconnect/core";
import { CkbWallet, getSdkError, CKBWalletAdapter, GetAddressesParams, SignTransactionParams, SignMessageParams } from "ckb-walletconnect-wallet-sdk";

class Adapter implements CKBWalletAdapter {
  async ckb_getAddresses(params: GetAddressesParams) {
    // ...
  }

  async ckb_signTransaction(params: SignTransactionParams) {
    // ...
  }

  async ckb_signMessage(params: SignMessageParams) {
    // ...
  }
}

const core = new Core({
  projectId: process.env.PROJECT_ID,
});

const walletSDK = await CkbWallet.init({
  core, 
  metadata: {
    name: "MyWalletnName",
    description: "My Wallet description",
    url: "https://mywalletdescription.com",
    icons: ["https://mywalletdescription.com/mywalleticon.png"],
  },
  adapter: new Adapter()
});
```

- Connect

```javascript
await walletSDK.connect(uri);
```
- Proposals

```javascript
const proposals = walletSDK.proposals
```

- Sessions

```javascript
const sessions = walletSDK.sessions
```

- Session Requests

```javascript
const requests = walletSDK.requests()
```

- Session Approval

```javascript
const session = await walletSDK.approve({
  id: proposal.id,
  chain: 'testnet',
  identity: 'xxxxxxxxxx',
  scriptBases:  ['0x9bd7e06f3ecf4be0f2fcd2188b23f1b9fcc88e5d4b65a8637b17723bbda3cce8']
});
```

- Session Rejection

```javascript
const session = await walletSDK.reject(proposal.id);
```

- Session Disconnect

```javascript
await walletSDK.disconnect(session.topic);
```

- Responding to Session Requests

```javascript
// Approve
await walletSDK.approveRequest(request);

// or Reject
await walletSDK.rejectRequest(request);

```

- Emit Session Events

```javascript
await walletSDK.emitSessionEvent({
  topic,
  event: {
    name: "accountsChanged",
    data: ["xxxxxxxxxx"],
  },
  chainId: "ckb:testnet",
});
```

- Handle Events

```javascript
walletSDK.emitter.on("proposals", handler);
walletSDK.emitter.on("sessions", handler);
walletSDK.emitter.on("requests", handler);
```
