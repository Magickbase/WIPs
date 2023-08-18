---
Number: '0047'
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

Users will encounter two namespaces: the proposal namespace and the session namespace when connecting wallets and dApps. Upon approval, the wallet will then send a session namespace that includes all the accounts, chains, methods, and events it has approved.

## Specification

Here is an example of a session approval message passing the namespace.

```
{
  ...
    "namespaces": {
      "ckb": {
        "chains": [ "ckb:mainnet", ... ],
        "accounts": [ "ckb:mainnet:${account_id}" ],
        "methods": [ "ckb_signTransaction", ... ],
        "events": [ "accountChanged", ... ],
      }
    }
  ...
}
```

### chains

A general blockchain identification schema has been defined in [CAIP-2](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-2.md). Its implementation for the CKB blockchain is as stated below

| Component           | Description                       |
| ------------------- | --------------------------------- |
| caip2-like chain_id | namespace + : + chain_reference   |
| namespace           | ckb                               |
| chain_reference     | One of (mainnet, testnet, devnet) |

### accounts

> CAIP-10 defines a way to identify an account in any blockchain specified by CAIP-2 blockchain id.

For context, see the [CAIP-10](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md) specification.

| Component              | Description                                      |
| ---------------------- | ------------------------------------------------ |
| caip10-like account_id | namespace + : + chain_reference + : + identity   |
| namespace              | ckb                                              |
| chain_id               | One of (mainnet, testnet, devnet)                |
| identity               | The hash obtained by the first pk of the account |

### methods

#### ckb_getAddresses

Get the active address list by specific lock script base[^1], in the format of `<code_hash>:<hash_type>`.

- Parameters

```
{
    [<script_base>] :{
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
    '<script_base a>' : Address[],
    '<script_base b>' : Address[],
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

## Further reading

1. [Reference-level explanation](./Reference-level-explanation.md)
2. Namespaces https://docs.walletconnect.com/2.0/specs/clients/sign/namespaces

## Refs

[^1]: Script Base: It's the combination of `code_hash` and `hash_type` of a specific `script` in the format `<code_hash>:<hash_type>`. Without a concrete `args` of the `script`, the `script base` refers to the set of an abstract script. The relation between `script base` and `script` can be considered as `class` and `instance`.
