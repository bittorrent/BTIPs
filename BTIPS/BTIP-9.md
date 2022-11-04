
```btip: 9
title: Vault contract support multi-coin
author: laocheng-cheng<laocheng.cheng@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/9
status: Idea
type: Core Protocol
category (*only required for Core Protocol): Vault contract
created: 2022-10-21
```

## Simple Summary

We will need a mechanism to get the exchange rates between WBTT and the other tokens(TRX/USDT/USDD) if we want to support multi-tokens.

## Abstract

Currently in BTFS, Upload files at the specified storage price of xxx WBTT/G/Month, and now plans to support more token prices. This gives you more options when uploading files on renters.

## Motivation

If multi-tokens payment scheme is implemented, it is more prominent how the storage price of multi-currency is set.

It is assumed that the storage price of BTFS is fixed (xxx WBTT/G/Month), and the prices of other tokens are used as a reference and set to floating exchange rates with reference to the market situation. Then it is possible to calculate different token prices in comparative real time.

## Specification

### 1.Price Oracle Contract:
- Set the WBTT as the base price. (xxx WBTT/G/Month)

- Set other token(TRX/USDD/USDT) and WBTT conversion exchange rate. The price of other tokens will be calculated according to the exchange rate between them and WBTT.

- Update the exchange rate value daily or regularly.

### 2.Exchange rate reference
The exchange rate will refer to the price of some large trading market sites, such as coinbase, huobi, binance.

## Rationale

TODO

## Backwards Compatibility

true

## Test Cases

## Implementation

TODO
