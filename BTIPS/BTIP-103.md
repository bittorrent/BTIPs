```
BTIP: 103
title: Support fetching SP address from the proposal contract for file uploads
author: codymeng<cody.meng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/103
status: Review 
type: Core Protocol
category (*only required for Core Protocol):
created: 2025-07-29
```

## Simple Summary

Enable BTFS clients to fetch Storage Provider (SP) addresses from the proposal contract, improving the integration between governance and storage operations.

## Abstract

This BTIP proposes enhancing the file upload process by allowing BTFS clients to query the proposal contract for available Storage Provider addresses. This integration ensures that file uploads can dynamically select from approved SPs based on governance decisions, creating a more decentralized and transparent storage allocation mechanism.

## Motivation

Currently, BTFS clients rely on APIs to fetch SP addresses instead of querying the decentralized proposal contract, limiting governance integration and decentralization.

## Specification

## Rationale

This creates direct integration between governance decisions and storage operations, ensuring approved SPs are utilized while maintaining decentralization.

## Backwards Compatibility

This proposal introduces a breaking change requiring clients to fetch SP addresses from the proposal contract; older clients will become incompatible.

## Test Cases

## Implementation
