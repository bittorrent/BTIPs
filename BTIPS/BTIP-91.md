```
BTIP: 91
title: Integrate an SP management smart contract to facilitate the lifecycle management of SP nodes
author: codymeng<cody.meng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/91
status: Idea
type: Core Protocol
category (*only required for Core Protocol):
created: 2025-05-22
```

## Simple Summary

Integrate a Storage Provider (SP) management smart contract to realize the lifecycle management of SP nodes, including registration, status change and deregistration, thereby enhancing the automation and transparency of the network.

## Abstract

This proposal suggests deploying an SP management smart contract on the BTTC network, responsible for the full lifecycle management of SP nodes, including registration, status change and deregistration. By automating processes and ensuring on-chain transparency through smart contracts, the efficiency and security of SP node management will be improved.

## Motivation

By introducing an SP management smart contract, the following can be achieved:

- Automate processes such as SP node registration, status change, and deregistration
- Execute incentive and penalty mechanisms on-chain to enhance fairness
- Make node status and history information queryable on-chain, increasing transparency

## Specification

```js
// Status constants
enum SPStatus { Pending, Rejected, Active, InActive }

struct SPInfo {
    string SPId;
    address Address;
    address ValidatorAddress;
    string Name;
    SPStatus status;
    string Description;
    uint256 lastUpdate;
}

mapping(address => SPInfo) public spNodes;
SPInfo[] public allSPNodes;

// Register SP node
function registerSP(address nodeAddr, uint256 stake) external;
// Status change
function updateStatus(address nodeAddr, SPStatus newStatus) external;
// Deregistration
function exitSP(address nodeAddr) external;
// Query
function getSPInfo(address nodeAddr) external view returns (SPInfo memory);

// Events
event SPRegistered(address indexed nodeAddr, uint256 stake);
event StatusUpdated(address indexed nodeAddr, SPStatus newStatus);
event SPExited(address indexed nodeAddr);
```

## Rationale

The SP management smart contract enables efficient, transparent, and automated lifecycle management of SP nodes.

## Backwards Compatibility

This new feature is backward-compatible and won’t cause breaking changes.

## Test Cases

## Implementation
