# NFTCollectionFactory.sol
L206, L230 The comment `// Version cannot overflow 256 bits.` is misleading as referenced variable is uint32.
https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L206
https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L230

# MarketFees.sol
**Inconsistent use of `BASIS_POINTS` and hardcoding.**
`CREATOR_ROYALTY_DENOMINATOR` is calculated (L45) using both the constant `BASIS_POINTS` but its final value is determined by hardcoding. It is used to divide an actual uint value and as such is independent from `BASIS_POINTS` which represents a whole. Usage here of `BASIS_POINTS` is at best redundant and misleading, and at worst an imprudent change of its value would cause an unintended change in `CREATOR_ROYALTY_DENOMINATOR`. Replace L45 with `uint256 private constant CREATOR_ROYALTY_DENOMINATOR = 10; // 10%`, and similarly for L47.
https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L45-L47

# FETHNode.sol
**Potential for reentrancy.**
If `_tryUseFETHBalance` (FETHNode.sol L46-L63) is called such that the else-if-condition at L56 passes then `msg.sender` can reenter at L60. This is not currently an issue as it is never called in this way, but if, for example, in NFTDropMarketFixedPriceSale.sol the function `mintFromFixedPriceSale` (L170-L219) had allowed a surplus payment (i.e. `msg.value > mintCost` does not revert at L201) with the intention to then refund it by replacing L204 with `_tryUseFETHBalance(mintCost, true);`, which would seem reasonable, then an attacker could reenter to buy more NFTs before they are minted to him and his balance is updated, such that he can exceed the `saleConfig.limitPerAccount` checked for at L183.
https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/FETHNode.sol#L46-L63
https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L170-L219