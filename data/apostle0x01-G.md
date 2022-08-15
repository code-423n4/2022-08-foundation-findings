## [G-01] Don't Initialize Variables with Default Value

#### Impact
Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecesary gas.

#### Findings:
```
contracts/PercentSplitETH.sol::115 => for (uint256 i = 0; i < _shares.length; ++i) {
contracts/PercentSplitETH.sol::135 => for (uint256 i = 0; i < shares.length; ++i) {
contracts/PercentSplitETH.sol::320 => for (uint256 i = 0; i < shares.length; ++i) {
contracts/libraries/BytesLibrary.sol::25 => for (uint256 i = 0; i < 20; ++i) {
contracts/libraries/BytesLibrary.sol::44 => for (uint256 i = 0; i < 4; ++i) {
contracts/mixins/shared/MarketFees.sol::126 => for (uint256 i = 0; i < creatorRecipients.length; ++i) {
contracts/mixins/shared/MarketFees.sol::198 => for (uint256 i = 0; i < creatorShares.length; ++i) {
contracts/mixins/shared/MarketFees.sol::484 => for (uint256 i = 0; i < creatorRecipients.length; ++i) {
```


## [G-02] Cache Array Length Outside of Loop

#### Impact
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.


#### Findings:
```
contracts/mixins/shared/MarketFees.sol::126 => for (uint256 i = 0; i < creatorRecipients.length; ++i) {
contracts/mixins/shared/MarketFees.sol::198 => for (uint256 i = 0; i < creatorShares.length; ++i) {
contracts/mixins/shared/MarketFees.sol::484 => for (uint256 i = 0; i < creatorRecipients.length; ++i) {
contracts/mixins/shared/MarketFees.sol::503 => for (uint256 i = 1; i < creatorRecipients.length; ) {
```

## [G-03] Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.


#### Findings:
```
contracts/NFTDropCollection.sol::131 => require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
```

## [G-04] Long Revert Strings

#### Impact
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.
If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.


#### Findings:
```
contracts/NFTCollection.sol::158 => require(tokenCreatorPaymentAddress != address(0), "NFTCollection: tokenCreatorPaymentAddress is required");
contracts/NFTCollection.sol::263 => require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");
contracts/NFTCollection.sol::264 => require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");
contracts/NFTCollection.sol::268 => require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
contracts/NFTCollection.sol::327 => require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");
contracts/NFTCollectionFactory.sol::173 => require(rolesContract.isAdmin(msg.sender), "NFTCollectionFactory: Caller does not have the Admin role");
contracts/NFTCollectionFactory.sol::182 => require(_rolesContract.isContract(), "NFTCollectionFactory: RolesContract is not a contract");
contracts/NFTCollectionFactory.sol::203 => require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
contracts/NFTCollectionFactory.sol::227 => require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
contracts/NFTCollectionFactory.sol::262 => require(bytes(symbol).length != 0, "NFTCollectionFactory: Symbol is required");
contracts/NFTDropCollection.sol::88 => require(bytes(_baseURI).length > 0, "NFTDropCollection: `_baseURI` must be set");
contracts/NFTDropCollection.sol::93 => require(postRevealBaseURIHash != bytes32(0), "NFTDropCollection: Already revealed");
contracts/NFTDropCollection.sol::130 => require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");
contracts/NFTDropCollection.sol::131 => require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
contracts/NFTDropCollection.sol::172 => require(count != 0, "NFTDropCollection: `count` must be greater than 0");
contracts/NFTDropCollection.sol::179 => require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");
contracts/NFTDropCollection.sol::238 => require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");
contracts/PercentSplitETH.sol::136 => require(shares[i].percentInBasisPoints < BASIS_POINTS, "Split: Share must be less than 100%");
contracts/PercentSplitETH.sol::148 => require(total == BASIS_POINTS, "Split: Total amount must equal 100%");
contracts/mixins/collections/SequentialMintCollection.sol::74 => require(totalSupply() == 0, "SequentialMintCollection: Any NFTs minted must be burned first");
contracts/mixins/collections/SequentialMintCollection.sol::87 => require(_maxTokenId != 0, "SequentialMintCollection: Max token ID may not be cleared");
contracts/mixins/collections/SequentialMintCollection.sol::88 => require(maxTokenId == 0 || _maxTokenId < maxTokenId, "SequentialMintCollection: Max token ID may not increase");
contracts/mixins/collections/SequentialMintCollection.sol::89 => require(latestTokenId <= _maxTokenId, "SequentialMintCollection: Max token ID must be >= last mint");
```