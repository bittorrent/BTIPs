
```
BTIP: 51
title: Add File metadata to blockchain
author: Shawn-Huang-Tron<shawn.huang@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/51
status: Last Call
type: Core Protocol
category (*only required for Core Protocol):
created: 2023-09-28
```

## Simple Summary

Currently, the primary method of searching for files or directories uploaded by users to BTFS is through the use of CIDs (Content Identifiers). These CIDs are complex strings that are not easily remembered, making them impractical for memorization. As a result, they are typically stored and accessed through copy-pasting actions or by embedding them into URLs.  However, this approach is not user-friendly, especially when trying to understand what types of files are available within the BTFS ecosystem.

Moreover, BTFS utilizes Distributed Hash Table (DHT) technology for routing and querying files via CIDs. While DHT technology offers a high degree of decentralization, eliminating the need for a single node for routing, it introduces latency issues, leading to a suboptimal user experience. A significant enhancement would be to develop methods that expedite the file access process or improve the indexing process for quicker queries.

To accomplish this, additional information, specifically metadata, is required. This metadata could be stored on centralized servers. It's important to acknowledge that for users, this approach may not fully address concerns regarding the authenticity of the data. However, considering that metadata requires less storage space and incurs lower costs, it is well-suited for storage on public blockchains. The decentralized nature and transparency of public blockchains make them an ideal solution for storing metadata related to the BTFS network, thereby enhancing the overall user experience.

This BTFS Improvement Proposal (BTIP) seeks to introduce a system that enables users to store comprehensive metadata alongside their files within the BTFS file system. This metadata includes essential details such as the Content Identifier (CID), file name, file type, and file size. Additionally, it proposes the inclusion of a storage certificate to facilitate smoother integration with decentralized applications (dApps) in the future. The proposal also encompasses storing pertinent information about the storage nodes, including their peer IDs, on the blockchain.The primary objective of this proposal is to enhance the efficiency of CID queries. By storing file metadata on the blockchain, it allows community members and developers to easily access this information by scanning the block and retrieving the data using natural semantics. This approach not only streamlines the process of retrieving file metadata but also significantly accelerates the overall process of querying CIDs within the BTFS ecosystem.

This BTIP recommends utilizing the BTTC chain for storing file metadata, as it offers lower storage costs compared to other public blockchains such as Ethereum. This can effectively reduce the expenses of on-chain operations for nodes.

## Abstract

The primary goal of this BTIP is to encourage nodes to store the metadata of uploaded files on the blockchain.

## Motivation

1. Once the file metadata is uploaded on the chain, semantic retrieval of file information can be achieved through methods such as block scanning;
2. By storing file metadata on the blockchain, we can swiftly locate the storage nodes associated with a file using its PeerID. This significantly speeds up the process of retrieving files.
3. File metadata adopts a decentralized storage approach, which enhances data availability and fosters user trust in BTFS.

## Specification

The cornerstone of the on-chain contract is a mapping data structure known as "fileMetaDataMap," detailed below.

```javascript
// map[cid][FileMetaData] --> fileMetaDataMap
mapping(string => FileMetaData) fileMetaDataMap;
struct FileMetaData {
    string ownerPeerId;
    string fileName;
    string fileExt;
    bool isDir;
    uint256 fileSize;
}
// event
event eventAddFileMeta(string cid, FileMetaData metaData);
event eventDeleteFileMeta(address from, string cid);
```

FileMetaData: This encapsulated data structure houses the metadata of a file. It includes details such as the node ID of the file owner, file name, file extension, file size, and an indication of whether the file is a directory, among other information.

For files that are directories, the metadata will feature an empty file extension (fileExt), and the isDir attribute will be set to true. To compute the total file size of a directory, it's necessary to recursively sum the sizes of all contained subdirectories and files.

fileMetaDataMap is a data structure that serves as the core of the contract, where the key is the file CID and the value is the corresponding FileMetaData. It stores the mapping relationship between each file and its metadata.
The contract provides the following methods:

```javascript
function AddFileMeta(string cid, FileMetaData metaData) external {
    emit eventAddFileMeta(cid, metaData);
}
 
function DeleteFileMeta(
    string cid,
    FileMetaData metaData
) external onlyValidator {
    emit eventDeleteFileMeta(cid);
}
 
function GetFileMeta(
    address from,
    string cid
) external view returns (bytes memory) {}
```

Here's a guide to uploading metadata onto the blockchain:

1. Initiate File Upload: Upload the file through the node using the standard "add file" method.
2. Prepare the Metadata: Encapsulate the metadata within the fileMetaDataMap data structure.
3. Interact with the Smart Contract: The node should then call the FileMetaData smart contract on the BTTC blockchain, utilizing the AddFileMeta method. This action associates the file's metadata with its CID on the blockchain.
4. Access Metadata Off-Chain: Once uploaded, all file metadata information can be retrieved through off-chain block scanning. This enables comprehensive analysis and facilitates the efficient retrieval of file information.

To upload metadata to the blockchain during the file upload process, nodes can use the following command. It's crucial to ensure that the node has enough gas fees for interacting with the on-chain contract:

```shell
btfs add --to-blockchain xxx
```

In this command, --to-blockchain is an optional parameter that defaults to false. So, if the user wants to upload the file's metadata to the chain, they need to manually add this parameter, as the uploading process incurs gas fees. This is also to prevent users from uploading sensitive file metadata.

The aforementioned "xxx" refers to the file itself, and adding a parameter does not change how the command was previously used.

## Backwards Compatibility

This new feature is backward-compatible and won’t cause breaking changes.

## Test Cases

## Implementation
