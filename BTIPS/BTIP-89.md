```
BTIP: 89
title: Introduce SPs as core storage and replacing the centralized guard with decentralized smart contracts
author: codymeng<cody.meng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/89
status: Idea
type: Core Protocol
category (*only required for Core Protocol):
created: 2025-05-15
```

## Simple Summary

This BTIP proposes a significant upgrade to the core operational model of BTFS. The main changes include:

1.  Formally introducing Storage Providers (SPs) as core nodes responsible for data storage and service provision within the network.
2.  Replacing the existing centralized guard service with a decentralized smart contract mechanism to enhance the network's decentralization, transparency, and security.

## Abstract

This BTIP proposes upgrading the BTFS operational model by introducing Storage Providers (SPs) as dedicated storage nodes and replacing the centralized guard service with smart contracts. These changes will enhance the network’s level of decentralization, transparency, and security.

## Motivation

The current BTFS relies on a centralized guard service to manage storage nodes, creating single points of failure and transparency issues. Introducing SPs and smart contracts enables on-chain governance, transparent and auditable operations, attracting higher quality storage resources and improving overall service quality.

## Specification

### 1. Storage Providers (SPs)

#### 1.1 Definition and Role

A Storage Provider (SP) is a registered and verified node in the BTFS network whose primary responsibility is to securely and reliably store user data and provide data access services on demand. SPs are the main contributors of storage resources in the network.

#### 1.2 Entry Conditions

- **Staking Requirement**: Potential SPs need to stake a certain amount of BTT.
- **Hardware and Network Requirements**: SPs may need to meet minimum hardware configurations (such as storage space, CPU, memory) and network bandwidth requirements to ensure service quality.
- **Registration Process**: SPs may also need to complete registration by submitting necessary node information.

#### 1.3 Responsibilities and Obligations

- **Data Storage**: Securely store user-uploaded data shards.
- **Data Accessibility**: Ensure users can retrieve their stored data on demand.

#### 1.4 Incentive Mechanism

- **Storage Rewards from Renters**: Receive storage payments directly from renters based on the storage contract terms, including storage amount, duration, and quality of service.
- **Airdrop Rewards**: Eligible to receive periodic airdrops of tokens as additional incentives for maintaining high-quality storage services and contributing to network growth.

### 2. Contract-Based Guard Service Replacement

A set of one or more smart contracts will replace the functionality of the existing centralized "guard" service. These contracts will be deployed on a compatible blockchain (e.g., BTTC network).

Below are some core data structures of the contract:

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

- **Reasons for Choosing the SP Model**: The SP model clearly defines the responsibilities and incentives for storage providers, helping to attract professional storage operators to join, thereby improving the overall storage quality and reliability of the network. Compared to unstructured node networks, the SP model is easier to manage and scale.
- **Advantages of Smart Contracts**: All transactions and state changes are publicly verifiable.
- **Necessity of Staking Mechanism**: The stake serves as an economic incentive and a basis for penalties, ensuring SPs have sufficient motivation to participate honestly in the network and provide quality service, reducing the risk of malicious behavior.

## Backwards Compatibility

This BTIP introduces significant changes to the core operational model of BTFS and is expected to have incompatible impacts on existing clients and nodes.

- **Renter**: Will need to be updated to support interaction with the new SP and smart contract system. Older clients may not function correctly with the new storage network.
- **Host**: Existing storage nodes will need to upgrade and register/operate according to the new SP specifications; otherwise, they will not be able to participate in the new network and receive incentives.

## Test Cases

## Implementation

```

```
