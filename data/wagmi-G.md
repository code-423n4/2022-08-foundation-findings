## 1. Use memory variable instead of storage in event to save gas

In function, if there are 2 variable with the same values, but 1 in memory and 1 in storage, we should use memory one when reading.

For example, [this event line](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L156)
```
emit CreateFixedPriceSale(nftContract, saleConfig.seller, saleConfig.price, saleConfig.limitPerAccount);
```

`saleConfig` is storage variable and these values can be replaced like `saleConfig.seller = msg.sender` to save gas

### Affected Codes

- https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L156

