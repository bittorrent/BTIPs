
```btip: 1
title: Integrate with bittorrent
author: Shawn-Huang-Tron<shawn.huang@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/1
status: Idea
type: Client API
category (*only required for Core Protocol):
created: 2022-09-30
```

## Simple Summary (required)

BTFS adds a new command/API to downloads the contents of a specific bittorrent seed.

## Abstract (required)

BTFS should integrate with the bittorrent libs to serve as a bittorrent client, and communicate with the tracker, DHT or bittorrent peers.Then downloads the content, if it was existed in BTFS already, then just download via BTFS network, after downloading completed, stop the communications with those p2p.

## Motivation (required)

This BTIP can help the BTFS network integrate with the bittorrent network. And rich the BTFS's ecosystems. After that, may be a lot of people will transfer their bittorrent's storage to the BTFS.

## Specification (required)

```shell
btfs bittorrent download -t torrent-file.torrent
```

## Rationale (required)

It based on the similarity between the BTFS and bittorrent.And the bittorrent is widely known.

## Backwards Compatibility (optional)

true

## Test Cases (optional)



## Implementation (required)

The implementations must be completed before any BTIP is given status "Final", but it need not be completed before the BTIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.
