```
BTIP: 79
title: Support filtering specific CIDs when accessing CIDs via the gateway
author: codymeng<cody.meng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/79
status: Draft
last-call-deadline: 2024-12-09
type: Client API
category (*only required for Core Protocol):
created: 2024-11-28
```

## Simple Summary

Add a command to add CIDs into the datastore, and some other commands to view or delete these CIDs. Then, we can implement a feature to filter these added CIDs when accessing CIDs through the gateway, ensuring that the resources corresponding to these CIDs cannot be accessed via the gateway.

## Abstract

Provide an effective method to prevent users from accessing specific CIDs through the gateway.

## Motivation

When someone exposing their CID resources through the gateway api, but there may be some certain resources, which could be harmful to users, and they may don't want them to be accessed by the api. In such cases, this feature can be used to block access to those resources without deleting them from the entire BTFS network.

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

## Backwards Compatibility

## Test Cases

## Implementation
