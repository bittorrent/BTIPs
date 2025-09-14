title: Magnet Link Swarm Support
author: George Talamantez
status: Draft
type: Core Protocol
category: Networking
created: 2025-09-14
Simple Summary
This proposal introduces support for BitTorrent-style magnet links and parallel, multi-peer "swarm" downloading to the BitTorrent File System (BTFS) to increase download speeds and file resilience.
 
Abstract
This BTIP proposes an extension to the BTFS protocol to enable swarm-based file transfers initiated via a magnet link. Currently, BTFS retrieves content from individual peers discovered through the DHT. This proposal specifies a mechanism for peers to advertise the specific file pieces they hold, allowing a client to discover multiple sources and download these pieces in parallel. This leverages the existing libp2p networking stack and DHT to create a more robust, efficient, and decentralized content delivery system within BTFS, mirroring one of the core strengths of the BitTorrent protocol.
 
Motivation
The current BTFS protocol, while effective for decentralized storage and retrieval, does not fully optimize for the rapid distribution of large or popular files. Content is typically downloaded linearly from a peer discovered via the DHT. This process can be slow if the hosting peer has limited upload bandwidth or if only a few peers hold the complete file.
The BitTorrent protocol solved this problem with "swarms," where each downloader also becomes an uploader for the pieces it possesses. Adopting this model in BTFS would:
1. 
Increase Download Speed: By pulling pieces from many peers in parallel, clients can aggregate bandwidth and complete downloads much faster.
2. 
Enhance File Availability: A file remains available as long as all its pieces exist somewhere in the network, even if no single peer holds the complete file.
3. 
Improve Network Efficiency: It promotes a "tit-for-tat" sharing ecosystem, reducing the load on any single host.
Without this feature, BTFS remains less efficient than BitTorrent for high-demand content distribution, limiting its potential use cases.
 
Specification
1. Magnet Link Format
A BTFS magnet link will be defined to identify the content and optionally provide discovery hints. The format is as follows:
btfs://<root-hash>?dn=<display-name>&xt=urn:btfs:<root-hash>
• 
btfs://<root-hash>: The primary content identifier, using the file's root CID.
• 
dn (Display Name): A user-friendly filename for the content.
• 
xt (eXact Topic): The URN specifying the BTFS content hash for multi-protocol compatibility.
2. Piece Advertisement on the DHT
Peers holding one or more pieces of a file will advertise their availability on the libp2p DHT.
• 
A new provider record type will be used for partial content.
• 
The DHT key will be derived from the file's root hash.
• 
The DHT value will contain the peer's ID and a bitfield (or similar compact data structure) indicating which pieces of the file they have available. This allows requesting peers to know exactly who to ask for a specific piece.
3. Peer Discovery and Piece Exchange
The download process is as follows:
1. 
Initiation: The client parses the btfs:// magnet link.
2. 
Peer Discovery: The client queries the DHT using the file's <root-hash> to find peers advertising pieces.
3. 
Piece Selection: The client aggregates the piece bitfields from all discovered peers to build a map of piece availability across the swarm. It then requests missing pieces from different peers in parallel.
4. 
Verification: Upon receiving a piece, the client verifies its integrity by hashing it and checking it against the file's manifest (the DAG structure of the root hash).
5. 
Re-advertisement: Once a client successfully downloads and verifies a piece, it updates its own bitfield and advertises its new status on the DHT, becoming a seeder for that piece for others in the swarm.
 
Rationale
The design choices were made to maximize compatibility with the existing BTFS/libp2p stack while introducing proven concepts from BitTorrent.
• 
Why use the DHT for piece advertisement? Using the existing libp2p DHT is a decentralized and tracker-less approach, which aligns perfectly with the ethos of BTFS. The alternative, using centralized trackers, would introduce a single point of failure and is contrary to the protocol's goals.
• 
Why a bitfield for piece availability? A bitfield is a highly compact and efficient data structure for representing which pieces a peer has. It has been proven effective in the BitTorrent protocol for decades and is trivial to implement and parse.
• 
Why not invent a new link format? The proposed btfs:// format is an extension of the existing content addressing scheme and follows the URN syntax established by BEP-9 for magnet links, making it both familiar and extensible.
This design directly addresses the motivation by creating a clear path for parallel downloads and increased redundancy. The primary consideration was to avoid a radical redesign of BTFS, instead layering swarm capabilities on top of the existing content-addressed foundation.
 
Backwards Compatibility
This proposal is fully backwards-compatible.
• 
Clients that do not support this BTIP will ignore the swarm-specific DHT records. They will fall back to the existing DHT provider lookup mechanism, finding peers that have the entire file and downloading from them as they do today.
• 
Clients that do support this BTIP can still interact with older clients, as they can treat a peer advertising the full file as simply a peer with a complete bitfield.
No breaking changes are introduced to the core data structures or peer-to-peer connection protocols.
 
Test Cases (Optional)
1. 
A client with a magnet link should be able to download a file from a swarm of 5 peers, where each peer only has 20% of the file.
2. 
An old client and a new client should be able to download the same file from a peer seeding the complete file.
3. 
A client, after downloading a piece, should correctly advertise its availability on the DHT, and another peer should be able to download that piece from the new client.
 
Implementation
An implementation must be created before this BTIP can be considered "Final." A reference implementation could be developed as a module for a major BTFS client (like go-btfs). It would involve:
1. 
Modifying the DHT logic to support publishing and querying piece-specific availability records.
2. 
Implementing a "piece manager" to track swarm state, select pieces, and manage parallel connections.
3. 
Adding a handler for the btfs:// magnet link scheme.
