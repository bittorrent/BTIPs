```
BTIP: 79
title: Support filtering specific CIDs when accessing CIDs via the gateway
author: codymeng<cody.meng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/79
status: Review
last-call-deadline: 2024-12-09
type: Client API
category (*only required for Core Protocol):
created: 2024-11-28
```

## Simple Summary

Implement one command to add CIDs to the database and several other commands to view and remove these CIDs. This solution will filter out the added CIDs and make their associated files inaccessible when users attempt to access CIDs via the gateway.

## Abstract

Provide an effective solution to restrict user access to specific CIDs via the gateway.

## Motivation

Users may wish to avoid certain CID files that could pose risks to them when accessing publicly available CIDs through API gateways. The proposed feature prevents access to these files without requiring their removal from the BTFS network.

## Specification

### related commands

#### 1. add cid

```shell
btfs cidstore add <cid>
```

#### 2. del cid

```shell
btfs cidstore del <cid>
```

#### 3. list cid

```shell
btfs cidstore list
```

#### 4. has cid

```shell
btfs cidstore has <cid>
```

## Rationale

The command provides a convenient way to manage CIDs that need to be filtered in the gateway.

## Backwards Compatibility

This new feature is backward-compatible and won’t cause breaking changes.

## Test Cases

## Implementation
