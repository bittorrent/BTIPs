
```
BTIP: 73
title: Support setting up private keys with imported keystore files
author: Shawn-Huang-Tron<shawn.huang@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/73
status: Review
type: Core Protocol
category (*only required for Core Protocol):
created: 2024-05-07
```

## Simple Summary

BTFS nodes currently allow users to set up private keys by importing keys and mnemonics. This BTIP aims to expand the choices by supporting the import of [EIP-2335](https://eips.ethereum.org/EIPS/eip-2335) keystore files.

## Abstract

This BTIP enables users to use the private key encrypted in an imported keystore file as a node's private key.

## Motivation

Diversify the types of private keys imported into BTFS nodes.

## Specification

The original btfs init command allows importing parameter -k to specify the type of the node's private key, and -i to specify the hex-encoded value of the private key during node initialization.  Below are the options for the -k parameter:

- RSA
- E25519
- Secp256k1
- ECDSA
- BIP39

This BTIP introduces another option for the -k parameter: keystore files.

This new option comes with an additional parameter, keypath, which specifies the path for the keystore file to be imported.

It also requires another parameter, pass, to specify the password of the keystore file to be decrypted.
Here is an example:

```shell
# Set up the private key with the imported keystore file for node initialization
btfs init -k=keystore -keypath=/home/ubuntu/UTC--2024-04-18T03-13-48.852713000Z--d95526169c208c76422ddf6abbb0eef318165a61 -pass=xxx
```

After running this command, the private key encrypted in the keystore file will be assigned as the node's private key.

## Backwards Compatibility

This new feature is backward-compatible and won’t cause breaking changes.

## Test Cases

## Implementation
