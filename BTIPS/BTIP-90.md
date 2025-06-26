```
BTIP: 90
title: Introduce smart contracts to manage and store file metadata
author: codymeng<cody.meng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/90
status: Last Call
last-call-deadline: 2025-07-03
type: Core Protocol
category (*only required for Core Protocol):
created: 2025-05-16
```

## Simple Summary

Introduce a smart contract to manage and store file metadata in a decentralized and transparent manner.

## Abstract

This BTIP proposes the introduction of a smart contract to manage and store file metadata on-chain. This approach enhances transparency, auditability, and decentralization of file metadata management within the network.

## Motivation

Currently, file metadata management lacks on-chain transparency and auditability. By introducing a smart contract, file metadata can be managed in a decentralized, transparent, and tamper-resistant way, improving trust and reliability for all participants.

## Specification

A smart contract will be deployed on the BTTC network to manage and store file metadata. The contract will provide interfaces for adding file metadata, updating contract statuses, and querying metadata and status information. Below are some core data structures of the contract:

```javascript
    uint8 public constant STATUS_INIT = 0;
    uint8 public constant STATUS_COMPLETED = 1;

    mapping(string => bytes) private fileMeta;
    mapping(string => uint8) private contractStatus;
    mapping(string => string) private contractHost;

    struct ContractHostPair {
        string contractId;
        string host;
    }

    event FileMetaAdded(string indexed cid, bytes metaData);
    event StatusUpdated(
        string indexed contractId,
        uint8 status,
        uint256 timestamp
    );

```

The following are the external methods exposed by the contract:

```javascript
    /**
     * @dev Add file metadata and initialize contract statuses.
     * @param cid File content identifier.
     * @param metaData Protobuf serialized metadata.
     * @param pairs Array of contractId and host pairs.
     */
    function addFileMeta(
        string calldata cid,
        bytes calldata metaData,
        ContractHostPair[] calldata pairs
    ) external {
        fileMeta[cid] = metaData;
        for (uint256 i = 0; i < pairs.length; ) {
            contractStatus[pairs[i].contractId] = STATUS_INIT;
            contractHost[pairs[i].contractId] = pairs[i].host;
            unchecked {
                ++i;
            }
        }
        emit FileMetaAdded(cid, metaData);
    }

    /**
     * @dev Update contract status. Only host or owner can update.
     * @param contractId Contract ID.
     * @param status New status.
     */
    function updateStatus(string calldata contractId, uint8 status) external {
        require(
            status == STATUS_INIT || status == STATUS_COMPLETED,
            "Invalid status"
        );
        require(
            bytes(contractHost[contractId]).length > 0,
            "Contract does not exist"
        );
        string memory host = contractHost[contractId];
        require(
            keccak256(abi.encodePacked(msg.sender)) ==
                keccak256(abi.encodePacked(host)) ||
                owner() == msg.sender,
            "Not authorized"
        );
        contractStatus[contractId] = status;
        emit StatusUpdated(contractId, status, block.timestamp);
    }
```

Furthermore, the contract will also expose some `view` methods.

```javascript
    /**
     * @dev Get file metadata and contract statuses.
     * @param cid File content identifier.
     * @param contractIds Array of contract IDs.
     * @return metaData File metadata.
     * @return statuses Array of contract statuses.
     */
    function getFileMeta(
        string calldata cid,
        string[] calldata contractIds
    ) external view returns (bytes memory metaData, uint8[] memory statuses) {
        metaData = fileMeta[cid];
        statuses = new uint8[](contractIds.length);
        for (uint256 i = 0; i < contractIds.length; ) {
            statuses[i] = contractStatus[contractIds[i]];
            unchecked {
                ++i;
            }
        }
    }

    /**
     * @dev Get contract status.
     * @param contractId Contract ID.
     * @return status Contract status.
     */
    function getStatus(
        string calldata contractId
    ) external view returns (uint8 status) {
        status = contractStatus[contractId];
    }

    /**
     * @dev Get contract host.
     * @param contractId Contract ID.
     * @return host Host address.
     */
    function getHost(
        string calldata contractId
    ) external view returns (string memory host) {
        host = contractHost[contractId];
    }
```

## Rationale

## Backwards Compatibility

This proposal introduces a breaking change. All nodes should upgrade to interact with the new smart contract.

## Test Cases

## Implementation
