# BTIPs

The BTFS Improvement Proposal repository

BitTorrent File System Improvement Proposals (BTIPs) describe standards for the BTFS platform, including core protocol specifications and client APIs.

## Contributing

First review this README. Then clone the repository and add your BTIP to it. There is a template BTIP here. Then submit a Pull Request to this repository.

## BTIP status terms

- **Idea** - An idea that is pre-draft. This is not tracked within the BTIP Repository.
- **Draft** - The first formally tracked stage of an BTIP in development. An BTIP is merged by an BTIP Editor into the BTIP repository when properly formatted.
- **Review** - An BTIP Author marks an BTIP as ready for and requesting Peer Review.
- **Last Call** - This is the final review window for an EIP before moving to - FINAL. An BTIP editor will assign Last Call status and set a review end date (`last-call-deadline`), typically 14 days later. If this period results in necessary normative changes it will revert the BTIP to Review.
- **Final** - This BTIP represents the final standard. A Final EIP exists in a state of finality and should only be updated to correct errata and add non-normative clarifications.
- **Stagnant** - Any EIP in Draft or Review if inactive for a period of 3 months or greater is moved to Stagnant. An BTIP may be resurrected from this state by Authors or BTIP Editors through moving it back to Draft.
- **Withdrawn** - The BTIP Author(s) have withdrawn the proposed BTIP. This state has finality and can no longer be resurrected using this BTIP number. If the idea is pursued at later date it is considered a new proposal.
- **Living** - A special status for EIPs that are designed to be continually updated and not reach a state of finality.

## The standardization process about the status

![This is the process about the status](BTIP-process-update.jpeg)

## BTIP status type

|   type name   |                 description                 |
| :-----------: | :-----------------------------------------: |
| core protocol | this kind of BTIP will affect core protocal |
|  client api   |  this kind of BTIP will affect client api   |

## All BTIPs list

|   ID    |                                                   Title                                                   |            Author             |     Type      |     Category      |  Status   |
| :-----: | :-------------------------------------------------------------------------------------------------------: | :---------------------------: | :-----------: | :---------------: | :-------: |
| BTIP-1  |                           [Integrate With BitTorrent Protocol](BTIPS/BTIP-1.md)                           |  <shawn.huang@tron.network>   |  client api   |                   |  Living   |
| BTIP-2  |                          [Vault Contract Support Multi-Tokens](BTIPS/BTIP-2.md)                           | <laocheng.cheng@tron.network> | core protocal |  Vault contract   |   Final   |
| BTIP-9  |                     [Price Oracle Contract to Support Multi-Tokens](BTIPS/BTIP-9.md)                      | <laocheng.cheng@tron.network> | core protocal |  Vault contract   |   Final   |
| BTIP-20 |                          [Use merekle root for online proofs](BTIPS/BTIP-20.md)                           |  <shawn.huang@tron.network>   | core protocal |   Online Proof    |   Final   |
| BTIP-25 |                       [Integrate With S3-Compatible API Service](BTIPS/BTIP-25.md)                        |  <steve.zhang@tron.network>   | core protocal | S3-Compatible API |  Living   |
| BTIP-30 |                       [Simplify Backup and Recovery of BTFS Data](BTIPS/BTIP-30.md)                       |  <shawn.huang@tron.network>   |  client api   |                   |   Final   |
| BTIP-35 |                [Support Multibase Command To Encode/Decode The Content](BTIPS/BTIP-35.md)                 |  <shawn.huang@tron.network>   |  client api   |                   |   Final   |
| BTIP-51 |                            [Add File metadata to blockchain](BTIPS/BTIP-51.md)                            |  <shawn.huang@tron.network>   | core protocal |                   |   Final   |
| BTIP-52 |                              [File share with encryption](BTIPS/BTIP-52.md)                               |  <shawn.huang@tron.network>   | core protocal |                   |   Final   |
| BTIP-54 |                          [Decentralize node score calculation](BTIPS/BTIP-54.md)                          |  <steve.zhang@tron.network>   | core protocal |                   |   Draft   |
| BTIP-72 |               [Support symmetric encryption when sharing files with BTFS](BTIPS/BTIP-72.md)               |  <shawn.huang@tron.network>   | core protocal |                   |   Final   |
| BTIP-73 |             [Support setting up private keys with imported keystore files](BTIPS/BTIP-73.md)              |  <shawn.huang@tron.network>   | core protocal |                   |   Final   |
| BTIP-79 |         [Support filtering specific CIDs when accessing CIDs via the gateway.](BTIPS/BTIP-79.md)          |   <cody.meng@tron.network>    |  client api   |                   |   Final   |
| BTIP-85 |               [Support staking and penalize storage nodes that lose data](BTIPS/BTIP-85.md)               |  <shawn.huang@tron.network>   | core protocal |                   | Withdrawn |
| BTIP-89 |                             [Introduce SPs as core storage](BTIPS/BTIP-89.md)                             |   <cody.meng@tron.network>    | core protocal |                   |   Final   |
| BTIP-90 |              [Introduce smart contracts to manage and store file metadata](BTIPS/BTIP-90.md)              |   <cody.meng@tron.network>    | core protocal |                   |   Final   |
| BTIP-91 | [Introduce Proposal Mechanism and Implement BTFS Network Governance via Smart Contract](BTIPS/BTIP-91.md) |   <cody.meng@tron.network>    | core protocal |                   |   Final   |
