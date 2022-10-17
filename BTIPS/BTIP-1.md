
```btip: 1
title: Integrate with bittorrent
author: Shawn-Huang-Tron<shawn.huang@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/1
status: Draft
type: Client API
category (*only required for Core Protocol):
created: 2022-09-30
```

## Simple Summary

BTFS adds a new command/API to downloads the files of a specific bittorrent seed or a magnet URI scheme.

## Abstract

BTFS should integrate with the bittorrent libs to serve as a bittorrent client, and communicate with the tracker, DHT or bittorrent peers.Then downloads the content, after downloading completed, stop the communications with those p2p.

## Motivation

This BTIP can help the BTFS network integrate with the bittorrent network. And rich the BTFS's ecosystems. After that, may be a lot of people will transfer their bittorrent's storage to the BTFS.
Imagine these scenarios:

> - A BTFS host wants to download and save the bittorrent files so that he can reach it anywhere, then after this BTIP completed, he can find it via https://gateway.btfs.io/btfs/{cid} or another BTFS local gateway endpoint.
> - A bittorrent user that has not deployed the BTFS node can visit this file via https://gateway.btfs.io/btfs/{cid}.
> - Maybe some of the bittorrent user will try to view the BTFS as their alternatives, that will be very awesome for the BTFS ecosystem.

In all these scenarios, it will be good for BTFS ecosystem to transfer the bittorrent's file.

## Specification
### download a bittorrent file:

```shell
btfs bittorrent download -t torrent-file.torrent
```
### download a magnet url:
```shell
btfs bittorrent download 'magnet:?xt=urn:btih:KRWPCX3SJUM4IMM4YF5RPHL6ANPYTQPU'
```

## Rationale

BTFS is dirrent from BitTorrent but they have something same such as they are all distributed network and decentralized.When we integrate with bittorrent, we consider the following steps:
1. Parsing the magnetic links to bittorrent metadata if the target is a magnet url.
2. Parsing the bittorrent file to bittorrent metadata if the target is a bittorrent seed file.
3. Judge by the bittorrent metadata and choose how to get peers by tracker/dht/webseed/peer.
4. Split the bittorrent metadata into a list of pieces and download all this pieces independently from the peers.
5. We can see the process of downloading in the console.
6. When downloading is finished, add this file to the local BTFS node by 
```shell
btfs add file
```
7. Maybe we can add a more user-interactive friendly dashboard to view this process.
## Backwards Compatibility

true

## Test Cases



## Implementation

The implementations must be completed before any BTIP is given status "Final", but it need not be completed before the BTIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.
