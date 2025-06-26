```
BTIP: 89
title: Introduce SPs as core storage
author: codymeng<cody.meng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/89
status: Last Call 
last-call-deadline: 2025-07-03
type: Core Protocol
category (*only required for Core Protocol):
created: 2025-05-15
```

## Simple Summary

Formally introducing Storage Providers (SPs) as core nodes responsible for data storage and service provision within the network.

## Abstract

This BTIP proposes upgrading the BTFS operational model by introducing Storage Providers (SPs) as dedicated storage nodes. This can significantly improve file reliability.

## Motivation

Introducing Storage Providers (SPs) attracts higher-quality storage resources and enhances the overall service quality of the network.

## Specification

### 1. Storage Providers (SPs)

#### 1.1 Definition and Role

A Storage Provider (SP) is a registered and verified node in the BTFS network whose primary responsibility is to securely and reliably store user data and provide data access services on demand. SPs are the main contributors of storage resources in the network.

#### 1.2 Entry Conditions

- **BTTC Validator Prerequisite**: Only nodes that have already become BTTC validators are eligible to apply for SP status.
- **Application Process**: After becoming a BTTC validator, the node can apply to become an SP by submitting the required information for verification.

#### 1.3 Responsibilities and Obligations

- **Data Storage**: Securely store user-uploaded data shards.
- **Data Accessibility**: Ensure users can retrieve their stored data on demand.

#### 1.4 Incentive Mechanism

- **Storage Rewards from Renters**: Receive storage payments directly from renters based on the storage contract terms, including storage amount, duration, and quality of service.
- **Airdrop Rewards**: Eligible to receive periodic airdrops of tokens as additional incentives for maintaining high-quality storage services and contributing to network growth.

## Rationale

- **Reasons for Choosing the SP Model**: The SP model clearly defines the responsibilities and incentives for storage providers, helping to attract professional storage operators to join, thereby improving the overall storage quality and reliability of the network. Compared to unstructured node networks, the SP model is easier to manage and scale.
- **Necessity of Being Validator First**: Requiring SPs to first become validators ensures that only nodes with proven reliability and trustworthiness can participate as storage providers. This prerequisite helps maintain the quality and security of the storage network by reducing the risk of unreliable or malicious nodes.

## Backwards Compatibility

This BTIP introduces significant changes to the core operational model of BTFS and is expected to have incompatible impacts on existing clients and nodes.

Existing storage nodes will need to upgrade and register/operate according to the new SP specifications; otherwise, they will not be able to participate in the new network and receive incentives.

## Test Cases

## Implementation
