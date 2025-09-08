```
BTIP: 104
title: Support renewal for uploaded files
author: codymeng<cody.meng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/104
status: Last Call
last-call-deadline: 2025-09-22
type: Core Protocol
category (*only required for Core Protocol):
created: 2025-07-30
```

## Simple Summary

Introduce a renewal mechanism that enables users to extend the storage duration of previously uploaded files, either automatically via an upload parameter or manually through a dedicated command.

## Abstract

This BTIP proposes adding both automatic and manual renewal capabilities to the BTFS network. Users will be able to extend the storage duration of already uploaded files without re-uploading their content. Automatic renewal can be enabled at upload time using a parameter, while manual renewal can be triggered via a CLI command. This enhancement improves user experience, reduces bandwidth usage, and supports long-term file storage use cases.

## Motivation

Currently, users must re-upload files when their storage duration is about to expire, resulting in:

- Increased bandwidth and storage overhead.
- Inconvenience for users who wish to maintain long-term storage.

A renewal feature enables efficient, seamless, and cost-effective extension of file storage.

## Specification

### Automatic Renewal

- During file upload, users can enable automatic renewal by adding the `--autorenew` parameter.
- When enabled:
  - The system automatically renews the file before its expiration.
  - Renewal costs are deducted from the user’s vault.

### Manual Renewal

- A new CLI command is introduced:
  ```bash
  btfs storage upload renew <cid> --storage-length <additional-time>
  ```

## Rationale

- Reduces duplicate uploads and bandwidth waste.
- Enhances user experience for long-term archival use cases.
- Facilitates enterprise adoption requiring consistent data retention.

## Backwards Compatibility

This new feature is backward-compatible and won’t cause breaking changes.

## Test Cases

## Implementation
