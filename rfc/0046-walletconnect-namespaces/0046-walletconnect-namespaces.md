---
Number: "0046"
Category: Standards Track
Status: Proposal
Author: fefe@magickbase.com
Created: 2022-08-08
---

# WalletConnect Namepsaces Protocol

## Abstract

This RFC describes a namepsaces protocol which is a standardized object defined by the [Chain Agnostic Improvement Proposal (CAIP)](https://github.com/ChainAgnostic/CAIPs) that ensures a common industry standard for chain agnostic purposes.

## Motivation

WalletConnect is an open-source protocol that enables secure and seamless communication between decentralized applications (dApps) and wallets on various blockchains. By establishing a remote connection using end-to-end encryption, WalletConnect allows users to interact with dApps through their preferred wallet without exposing their private keys.

User will encounter two namespaces: the proposal namespace and the session namespace when connects wallets and dapps.Upon approval, the wallet will then send a session namespace which includes all of the chains, methods, and events it has approved.

## Specification

Here is an example of a session approval message, passing the namespace.

```
{
  ...
    "namespaces": {
      "ckb": {
        "accounts": [ "ckb:mainnet:xxxxx" ],
        "chains": [ "ckb:mainnet", ... ],
        "events": [ "accountChanged", ... ],
        "methods": [ "ckb_signTransaction", ... ],
      }
    }
  ...
}
```

### chains

In CAIP-2, ageneral blockchain identification schema is defined. This is the implementation of [CAIP-2](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-2.md) for the CKB network.


Component | Description
-- | --
caip2-like chain | namespace + : + chainId
namespace | ckb
chainId | One of (mainnet, testnet, devnet)

<a name="accounts"/>

### accounts

> CAIP-10 defines a way to identify an account in any blockchain specified by CAIP-2 blockchain id.

For context, see the [CAIP-10](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md) specification.

Component | Description
-- | --
caip10-like account | namespace + : + chainId + : + identity
namespace | ckb
chainId | One of (mainnet, testnet, devnet)
identity | The hash obtained by the first pk of the account

<a name="methods"/>

### methods

#### ckb_getAddresses

Get the active address list by specific lock script types

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

#### ckb_signTransaction

Get a transaction over the provided instructions.

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

#### ckb_signMessage

Get a signature for the provided message from the requested signer address.

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

<a name="events"/>

### events

#### accountChanged

Emit the event when the account changed

```
event: {
   name: "accountChanged",
   data: {
       account: string,
       addresses: Address[]
   },
}
```

#### addressesChanged

Emit the event when addresses changed

```
event: {
   name: "addressesChanged",
   data: {
        ...Address,
        changeType: 'add' | 'consume'
    }[]
}
```


#### chainChanged

Emit the event when the chain changed

```
event: {
   name: "chainChanged",
   data: string,
}
```

## Reference

1. [Reference-level explanation](./Reference-level-explanation.md)
2. Namespaces https://docs.walletconnect.com/2.0/specs/clients/sign/namespaces
