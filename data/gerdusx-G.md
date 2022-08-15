# Gas Optimazations
# [G-01] Short require strings save gas 
Strings in solidity are handled in 32 byte chunks. A require string longer than 32 bytes uses more gas. Shortening these strings will save gas.


There are 13 occurrences

**NFTCollection.sol**
[L158](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L158) `    require(tokenCreatorPaymentAddress != address(0), "NFTCollection: tokenCreatorPaymentAddress is required");`
[L263](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L263) `    require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");`
[L264](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L264) `    require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");`
[L327](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L327) `    require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");`

**NFTCollectionFactory.sol**
[L182](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L182) `    require(_rolesContract.isContract(), "NFTCollectionFactory: RolesContract is not a contract");`
[L203](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L203) `    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");`
[L227](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L227) `    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");`
[L262](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L262) `    require(bytes(symbol).length != 0, "NFTCollectionFactory: Symbol is required");`

**NFTDropCollection.sol**
[L130](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L130) `    require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");`
[L131](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L131) `    require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");`
[L172](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L172) `    require(count != 0, "NFTDropCollection: `count` must be greater than 0");`
[L179](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L179) `    require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");`
[L238](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L238) `    require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");`

# [G-02] Use Custom Errors instead of revert()/require() 
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)


There are 13 occurrences

**NFTCollection.sol**
[L158](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L158) `    require(tokenCreatorPaymentAddress != address(0), "NFTCollection: tokenCreatorPaymentAddress is required");`
[L263](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L263) `    require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");`
[L264](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L264) `    require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");`
[L327](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L327) `    require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");`

**NFTCollectionFactory.sol**
[L182](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L182) `    require(_rolesContract.isContract(), "NFTCollectionFactory: RolesContract is not a contract");`
[L203](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L203) `    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");`
[L227](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L227) `    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");`
[L262](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L262) `    require(bytes(symbol).length != 0, "NFTCollectionFactory: Symbol is required");`

**NFTDropCollection.sol**
[L130](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L130) `    require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");`
[L131](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L131) `    require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");`
[L172](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L172) `    require(count != 0, "NFTDropCollection: `count` must be greater than 0");`
[L179](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L179) `    require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");`
[L238](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L238) `    require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");`

