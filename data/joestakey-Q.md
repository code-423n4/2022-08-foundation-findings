# QA Report

## Table of Contents

### Summary

### Low

- [Minting does not work for ERC-1155 tokens](#minting-does-not-work-for-erc-1155-tokens)
- [Sales can be created even when sold out](#sales-can-be-created-even-when-sold-out)
- [Use of `_mint` can result in NFT loss](use-of-_mint-can-result-in-nft-loss)
- [Overflow can happen](overflow-can-happen)

# Summary

> The main concerns are with the use of DropMarket with custom NFT contracts. These custom contracts must only adhere to `INFTDropCollectionMint` and have a lot of liberties as to what logic they can perform, which can lead to incorrect state handling in a couple issues that are detailed in this report. 

# Minting does not work for ERC-1155 tokens

In `NFTDropMarketFixedPriceSale.createFixedPriceSale` the only requirement for `nftContract` is that it must adhere to `INFTDropCollectionMint`.
But in `NFTDropMarketFixedPriceSale.mintFromFixedPriceSale`, there is a check of the collector balance using `IERC721(nftContract).balanceOf(msg.sender)`. The function will always revert for a collection not adhering to `IERC721`.
This means it is possible to create a sale for a contract following the `ERC-1155` standard, for which the minting will not work.

## Impact

Low


## Proof Of Concept

See this [foundry test](https://gist.github.com/joestakey/a5dda854f0094ff30648da63fb569e9e) that manages to create a sale for an ERC-1155 NFT contract. Minting will systematically fail because of the [balanceOf check](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L183) in `mintFromFixedPriceSale()`.


## Tools Used

Manual Analysis, Foundry



## Mitigation

Two options:

1 - you choose to only support custom contracts adhering to `ERC-721`. 

You can add an extra check in `createFixedPriceSale()`
```diff
-124:   if (!nftContract.supportsInterface(type(INFTDropCollectionMint).interfaceId)) {
+124:   if (!nftContract.supportsInterface(type(INFTDropCollectionMint).interfaceId) || !nftContract.supportsInterface(type(IERC721).interfaceId)) {
```

2 - you choose to support `ERC-1155` tokens.

This would require re-writing the functions `createFixedPriceSale` and `mintFromFixedPriceSale`, because `saleConfig.limitPerAccount` and `count` do not allow to differentiate between `tokenId` and `amount`: when calling `mintFromFixedPriceSale(nftContract,count,)` for a ERC-1155 contract, it is not clear whether it should mint `count` tokens for a given token id or one instance of `count` tokens with different token ids.
The [balanceOf condition](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L183) would also need re-writing as `balanceOf` has 1 parameter in `ERC721` but 2 in `ERC1155`.


# Sales can be created even when sold out

`NFTDropMarketFixedPriceSale.createFixedPriceSale` allows creators to create a sale drop. This can be used for a custom NFT Contract that adheres to `INFTDropCollectionMint`. 
If `nftContract.numberOfTokensAvailableToMint()` is not implemented properly and does not return `0` when no more tokens are available to mint, the creator can call `NFTDropMarketFixedPriceSale.createFixedPriceSale` even after all the tokens in `nftContract` have been minted.

## Impact

Low


## Proof Of Concept

- Alice creates a `customERC721` contract adhering to `INFTDropCollectionMint`. She mistakenly writes `numberOfTokensAvailableToMint()` so that it returns the total supply of `customERC721`, instead of the remaining tokens to be minted.
- All `customERC721` tokens get minted in an external sale, leaving no more token available to mint.
- Alice calls (`NFTDropMarketFixedPriceSale.createFixedPriceSale(address(customERC721), price, limit)`). As `customERC721.numberOfTokensAvailableToMint()`, returns a positive value, the checks are passed and a sale for `customERC721` is created, writing the sale details in `nftContractToFixedPriceSaleConfig[customERC721]`.
- Bob is monitoring the `CreateFixedPriceSale` event. Upon noticing `CreateFixedPriceSale(customERC721, Alice, price, limit)`, he calls `NFTDropMarketFixedPriceSale.mintFromFixedPriceSale(customERC721, n < limit,)`, which ends up getting rejected.

Alice does not really have a reason to create a sale on the Drop Market, but the issue here lies in the fact that it is possible. The creation of a sale for a sold out NFT collection qualifies as incorrect state logic.
Additionally, as a `CreateFixedPriceSale` event is emitted upon sale creation, it can make things complicated for indexing tools, knowing that they can pick up obsolete sales.

## Tools Used

Manual Analysis



## Mitigation

The vulnerability lies in the fact that you have no control over the logic happening in `INFTDropCollectionMint(nftContract).numberOfTokensAvailableToMint()`. Hence the check `INFTDropCollectionMint(nftContract).numberOfTokensAvailableToMint() == 0` is not enough to guarantee a sale is not sold out.

A potential mitigation could be to replace the `INFTDropCollectionMint(nftContract).numberOfTokensAvailableToMint() == 0` check with a check of `ERC721(nftContract).ownerOf(total_supply)`. But this would only be a mitigation for ERC721 contracts doing sequential minting and would not work for ERC721 contract performing other types of minting.

# Use of `_mint` can result in NFT loss

In [`NFTDropCollection.mintCountTo()`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L182), `ERC721Upgradeable._mint()` is used.

As per the comments in the OZ contract:

```
WARNING: Usage of this method is discouraged, use {_safeMint} whenever possible
```

That is because in the case `to` is a smart contract, `_mint` does not check if the address receiving the NFT implements `onERC721Received()`. Thus, there is no check whether the receiving address supports ERC-721 tokens.

if such a contract calls `NFTDropMarketFixedPriceSale.mintFromFixedPriceSale()`, the NFT minted could be lost.

## Impact

Low


## Tools used

Manual Analysis



## Mitigation

Consider using [`_safeMint`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/895a6796ede10de2bf85deaa13dfaf58f2bf96fb/contracts/token/ERC721/ERC721Upgradeable.sol#L252) instead, which will provide an additional safety net for collectors.

That being said, using `_mint` instead of `safeMint` is a common approach to save gas and to avoid reentrancy issues, so it is understandable if the team want to keep things as they are. 



# Overflow can happen

Across the contracts in scope, several `uint32` state variables are incremented in an `unchecked` block, with comments stating they cannot overflow.

These are all assumptions and not impossibilities

## Impact

Low

## Tools used

Manual Analysis

### Proof Of Concept


In [NFTCollectionFactory.adminUpdateNFTCollectionImplementation()](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L207), if `versionNFTCollection == type(uint32).max`, this would overflow and `versionNFTCollection` would equal `0`, ie there would be version collision with two different `implementationNFTCollection`.
The same thing can happen for [versionNFTDropCollection](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L231).
`type(uint32).max == 4.294.967.295` so it is very unlikely to happen, but is not impossible.


There is a similar issue in [NFTCollection.mint()](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L278), where latestTokenId can also overflow - note that it would not result in any loss of asset as `_mint` would revert when trying to mint a token that has already been minted, but `latestTokenId` is a public state variable and it would be confusing for external users querying its value.

## Mitigation

Remove the `unchecked` keyword from these blocks.
