
```btip: <to be assigned>
title: <BTIP title>
author: <a list of the author's or authors' name(s) and/or username(s), or name(s) and email(s), e.g. (use with the parentheses or triangular brackets): FirstName LastName (@GitHubUsername), FirstName LastName <foo@bar.com>, FirstName (@GitHubUsername) and GitHubUsername (@GitHubUsername)>
discussions-to: <URL>
status: <Idea | Draft | Last Call | Accepted | Final | Withdrawn | Living>
type: <Core Protocol | Client API>
category (*only required for Core Protocol): <Core | Networking | Interface>
created: <date created on, in ISO 8601 (yyyy-mm-dd) format>
requires (*optional): <BTIP number(s)>
replaces (*optional): <BTIP number(s)>
```

This is the suggested template for new BTIPs.

Note that an BTIP number will be assigned by an editor. When opening a pull request to submit your BTIP, please use an abbreviated title in the filename.

The title should be 44 characters or less.

## Simple Summary (required)

Provide a simplified explanation of the BTIP.

## Abstract (required)

A short (~200 word) description of the technical issue being addressed.

## Motivation (required)

The motivation is critical for BTIPs that want to change the BTFS protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the BTIP solves. BTIP submissions without sufficient motivation may be rejected outright.

## Specification (required)

The technical specification should describe the syntax and semantics of any new feature. 

## Rationale (required)

The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.

## Backwards Compatibility (optional)

All BTIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The BTIP must explain how the author proposes to deal with these incompatibilities. BTIP submissions without a sufficient backwards compatibility treatise may be rejected outright.

## Test Cases (optional)

Test cases for an implementation are mandatory for BTIPs that are affecting consensus changes. Other BTIPs can choose to include links to test cases if applicable.

## Implementation (required)

The implementations must be completed before any BTIP is given status "Final", but it need not be completed before the BTIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.
