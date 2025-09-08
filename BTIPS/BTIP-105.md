```
BTIP: 105
title: Introduce Proxy Mode for File Upload
author: codymeng<cody.meng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/105
status: Last Call
last-call-deadline: 2025-09-22
type: Core Protocol
category (*only required for Core Protocol):
created: 2025-07-31
```

## Simple Summary

Introduce a proxy mode that enables file upload through an intermediate proxy node.  
This feature allows clients to upload files indirectly when direct connections to storage providers are not feasible.

## Abstract

The proxy mode introduces a dedicated upload routing mechanism where a client sends file data to a proxy node, which then forwards the upload to the designated storage provider (SP).  
This is particularly useful in network-restricted or NATed environments, improving accessibility and reliability for file uploads.

## Motivation

In some environments, clients are unable to establish direct connections to storage providers due to firewall rules, NAT, or other network restrictions.  
By adding proxy support, clients can route upload requests through a proxy node without modifying the underlying storage protocol.  
This feature increases network flexibility and supports a broader range of deployment scenarios.

## Specification

- **CLI & API Changes**

  - Add `--proxy <proxy-url>` CLI parameter to enable proxy mode and specify the proxy endpoint. Default behavior remains unchanged if `--proxy` is not provided.
  - Add `btfs storage upload proxy-pay` command to settle payment for proxy upload.

- **Proxy Node Behavior**

  - Accept client upload requests.
  - Forward file data to the designated SP.
  - Accept the BTT payment from the client and forward it to the SP.

## Rationale

Proxy mode enables users who cannot easily run a local BTFS node to upload files via a proxy node.
It lowers the barrier to entry, improves accessibility, and ensures proper payment settlement without requiring changes to the core BTFS protocol.

## Backwards Compatibility

This new feature is backward-compatible and won’t cause breaking changes..

## Test Cases

## Implementation
