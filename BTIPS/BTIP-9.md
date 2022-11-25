
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

The BTFS client fetch the storage price(WBTT) from the Price Oracle contract. To support multi-tokens, we need upgrade this contract to feed and query upload price of the other tokens(TRX/USDT/USDD).

## Abstract

Currently in BTFS, Upload files at the specified storage price of xxx WBTT/G/Month, and now plans to support more token prices. This gives you more options when uploading files on renters.

## Motivation

If multi-tokens payment scheme is implemented, it is more prominent how the storage price of multi-currency is set.

It is assumed that the storage price of BTFS is fixed (xxx WBTT/G/Month), and the prices of other tokens are used as a reference and set to floating exchange rates with reference to the market situation. Then it is possible to calculate different token prices in comparative real time.

## Specification
![btfs price_feed](../pictures/price-feed.png)

### 1.Price Oracle Contract:
- Add a mapping to save the token and the token's specified storage price. (token => price/G/Month)
```solidity
    mapping (address => uint256) public prices;
```

- Add a public method for contract owner to update these token's price 
```solidity
    function updatePrices(address[] calldata tokens, uint256[] calldata newPrices) external onlyOwner {
        require(tokens.length > 0, "length not grater than 0");
        require(tokens.length == newPrices.length, "length not match");
        for (uint256 i = 0; i < tokens.length; i++) {
            prices[tokens[i]] = newPrices[i];
        }
        emit PricesUpdate(tokens, newPrices);
    }
```

- Add a public method for BTFS client to query the specified token(s) price
```solidity
    function getPrices(address[] calldata tokens) external view returns (uint256[] memory result) {
        require(tokens.length > 0, "tokens length not greater than 0");
        result = new uint256[](tokens.length);
        for (uint256 i = 0; i < tokens.length; i++) {
            result[i] = prices[tokens[i]];
        }
    }
```
### 2.Price Feed Service
- Configure the storage price of WBTT as the fixed price
```
storage_price_of_WBTT = <fixed_price_number>;
```

- Fetch the exchange rate From WBTT to the other tokens(TRX/USDT/USDD) from third party platform regularly
```
exchange_rate_of_WBTT_to_TRX = getExchangeRate('WBTT', 'TRX');
storage_price_of_TRX = storage_price_of_WBTT * exchange_rate_of_WBTT_to_TRX; 
```

- Call the Price Oracle contract to update the storage price(exchange rate * WBTT's storage price) of all tokens
```
PriceOracleContract.UpdatePrices([<WBTT_token_address>, <TRX_token_address>], [<storage_price_of_WBTT>, <stroage_price_of_TRX>]);
```
## Rationale

TODO

## Backwards Compatibility

true

## Test Cases

## Implementation

TODO
