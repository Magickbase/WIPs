# Introduction
This article will present the usage of `CKB's WalletConnect SDK`, an auxiliary library built on top of WalletConnect which wraps the protocol for integration within the CKB ecosystem.

## Why do I need WalletConnect in my dApp?
Almost every decentralized application needs user's authentication to send a signed transaction to the blockchain. From minting tokens to making a simple transfer, users must always sign their transactions whenever the client-side application needs to call a SmartContract method that requires the user's Account.

Without a solution like WalletConnect, the user would need to trust their private key to the dApp in order to sign. For obvious reasons, outside of testing environments, this is a huge security issue. The dApp could simply use the key to maliciously steal funds or sign something not approved by the user.

## Requirements
Create an account on Wallet Connect website and then create a new Project, it's super easy, with just a few fields on the form. After that, you will be able to get your projectId and use it on your application.

## Installation
Install the dependency on your client-side application

```
npm i @nervosnetwork/ckb-walletconnect-sdk @walletconnect/sign-client @web3modal/standalone
```

## Setup
Initialize the SDK with the following code:
```
import CkbWC from '@nervosnetwork/ckb-walletconnect-sdk'
import SignClient from '@walletconnect/sign-client'

const ckbWC = new CkbWC(await SignClient.init({
  projectId: '<project id>', // the ID of your project on Wallet Connect website
  metadata: {
    name: 'MyApplicationName', // your application name to be displayed on the wallet
    description: 'My Application description', // description to be shown on the wallet
    url: 'https://myapplicationdescription.app/', // url to be linked on the wallet
    icons: ['https://myapplicationdescription.app/myappicon.png'] // icon to be shown on the wallet
  }
}))
```

## Using the SDK

### Check if the user has a session
```
if (ckbWC.isConnected()) {
  console.log(ckbWC.getAccount()) 
  console.log(ckbWC.getChain()) 
  console.log(ckbWC.session.namespaces); 
}
```

### Connect to the Wallet

```
if (!ckbWC.isConnected()) {
  const { uri, approval } = await ckbWC.connect({
    chain: "ckb:testnet",
    scriptBases: [] // default: [] represent all types
  })  

  await web3Modal.openModal({ uri });
  await approval();

  console.log(ckbWC.isConnected() ? 'Connected successfully' : 'Connection refused')
}
```

### Disconnect
```
await ckbWC.disconnect();
```

#### Get Addresses

Get the active address list by specific lock script types

```
await ckbWC.getAddresses(params)
```
- Parameters
```
{
    [<lock code hash>] :{
       page: {
         size: number
         before: number
         after: number
       },
       type: 'derived' | 'all'
   }
}
```

- Returns
```
{
    '<lock code hash a>' : Address[],
    '<lock code hash b>' : Address[],
}
```

#### Sign Transaction

Get a transaction over the provided instructions.

```
await ckbWC.signTransaction(params)
```

- Parameters
```
{ 
  data: {
     cell_deps: {
       out_point: {
         tx_hash: string
         index: string
       }
       dep_type: string
     }[]
     header_deps: string[]
     inputs: {
       previous_output: {
         index: string
         tx_hash: string
       }
       since: string
     }[]
     outputs: {
     capacity: string
       lock: {
         args: string
         code_hash: string
         hash_type: 'type' | 'data'
       },
       type: {
         args: string
         code_hash: string
         hash_type: 'type' | 'data'
       }
     }[]
     outputs_data: string[]
  },
  description: string
}
```

- Returns
```
{
  transaction: Transaction
}
```

#### Sign Message

Get a signature for the provided message from the requested signer address.
```
const signature = await ckbWC.signMessage(params)
```
- Parameters
```
{
   message: string, // the message to sign
   address: string  // address of which private to sign
}
```

- Returns
```
{
  signature: string
}
```

#### Listen Account Changed Event

```
ckbWC.onAccountChanged((event) => {
  // console.log(event) 
})
```
- Event
```
{
  account: string,
  addresses: Address[]
}
```

#### Listen Addresses Changed Event

```
ckbWC.onAddressesChanged((event) => {
  // console.log(event) 
})
```
- Event
```
{
  ...Address,
  changeType: 'add' | 'consume'
}[]
```

#### Listen Chain Changed Event

```
ckbWC.onChainChanged((event) => {
  // console.log(event) 
})
```
- Event
```
string // chain
```
