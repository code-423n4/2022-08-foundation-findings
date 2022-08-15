# Summary

| Id | Title |
| -- | ----- |
| 1 | `postRevealBaseURIHash` is not used to validate `baseURI` |
| 2 | Users can always bypass the `limitPerAccount` config |
| 3 | Not subtract `referrerFee` in `getFeesAndRecipients()` |

## 1. `postRevealBaseURIHash` is not used to validate `baseURI`

In documentation, `postRevealBaseURIHash` is supposed to be used to validate `baseURI` by making sure `hash(baseURI) == postRevealBaseURIHash` when admin reveal the `baseURI`

But in implementaion (function `reveal()`), this value is not used for that purpose and have no value for on-chain validation.

### Affected Codes
- https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L195

## 2. Users can always bypass the `limitPerAccount` config

There is a config `limitPerAccount` in `saleConfig`. It is supposed to be used to confirm that the buyer will not exceed the limit specified after minting. 

But this kind of limit in general can always be bypassed by users. It checks that current balance of users add count should be smaller or equal to `limitPerAccount`

```solidity
if (IERC721(nftContract).balanceOf(msg.sender) + count > saleConfig.limitPerAccount) {
```

Users can transfer these ERC721 token to another wallet to modify value of `balanceOf` or they can simply use another wallet to bypass this check.

### Affected Codes

- https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L183

## 3. Not subtract `referrerFee` in `getFeesAndRecipients()`

In function `getFeesAndRecipients()`, there is an open TODO that should be update to add referral info. Otherwise, these function will return values without referrerFee and can harm user experience.

### Affected Codes
- https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L193
