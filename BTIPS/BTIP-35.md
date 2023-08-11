
```
BTIP: 35
title: Support Multibase Command To Encode/Decode The Content
author: Shawn-Huang-Tron<shawn.huang@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/35
status: Last Call
type: Client API
category (*only required for Core Protocol):
created: 2023-07-21
```

## Simple Summary

When streaming responses to clients, some of the HTTP APIs send newline-delimited JSON.

One typical example is `pubsub/sub`. It sends data as an ASCII-encoded string with escape characters, which might cause problems when the HTTP client parses the string (i.e. a newline character might cause the HTTP client to stop parsing the string).

The `btfs multibase` commands can solve this problem by encoding/decoding the content.

## Motivation

This feature aims to help users encode/decode documents and texts by providing different types of multibase commands.

## Specification

### btfs multibase

```shell
> btfs multibase  --help
USAGE
  btfs multibase - Encode and decode files or stdin with multibase format

SYNOPSIS
  btfs multibase

SUBCOMMANDS
  btfs multibase decode <encoded_file> - Decode multibase string
  btfs multibase encode <file>         - Encode data into multibase string
  btfs multibase list                  - List available multibase encodings.
```

### btfs multibase encode

```shell
> btfs multibase encode --help
USAGE
  btfs multibase encode <file> - Encode data into multibase string

SYNOPSIS
  btfs multibase encode [-b=<b>] [--] <file>

ARGUMENTS

  <file> - data to encode

OPTIONS

  -b  string - multibase encoding. Default: base64url.

DESCRIPTION

  This command expects a file name or data provided via stdin.

  By default it will use URL-safe base64url encoding,
  but one can customize used base with -b:

    > echo hello | btfs multibase encode -b base16 > output_file
    > cat output_file
    f68656c6c6f0a

    > echo hello > input_file
    > btfs multibase encode -b base16 input_file
    f68656c6c6f0a
```

### btfs multibase decode

```shell
> btfs multibase decode --help
USAGE
  btfs multibase decode <encoded_file> - Decode multibase string

SYNOPSIS
  btfs multibase decode [--] <encoded_file>

ARGUMENTS

  <encoded_file> - encoded data to decode

DESCRIPTION

  This command expects multibase inside of a file or via stdin:

    > echo -n hello | btfs multibase encode -b base16 > file
    > cat file
    f68656c6c6f

    > btfs multibase decode file
    hello

    > cat file | btfs multibase decode
    hello
```

### btfs multibase list

This is the same command as, or the alias of the existing command btfs cid bases. It is added here for better UX.

```shell
> btfs multibase list --help
USAGE
  btfs multibase list - List available multibase encodings.

SYNOPSIS
  btfs multibase list [--prefix] [--numeric]

OPTIONS

  --prefix   bool - also include the single letter prefixes in addition to the code.
  --numeric  bool - also include numeric codes.
```

```shell
> btfs multibase list --prefix --numeric
      0  identity
0    48  base2
b    98  base32
B    66  base32upper
c    99  base32pad
C    67  base32padupper
f   102  base16
F    70  base16upper
k   107  base36
K    75  base36upper
m   109  base64
M    77  base64pad
t   116  base32hexpad
T    84  base32hexpadupper
u   117  base64url
U    85  base64urlpad
v   118  base32hex
V    86  base32hexupper
z   122  base58btc
Z    90  base58flickr
```

## Backwards Compatibility

This new feature is backward-compatible and won’t cause breaking changes.

## Test Cases

## Implementation
