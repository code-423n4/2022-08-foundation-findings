1. Short reason string can be used for saving more gas

Every reason string takes at least 32 bytes. Use short reason strings that fits in 32 bytes or it will become more expensive.

File : 

```
/main/contracts/NFTCollection.sol#L158 "NFTCollection: tokenCreatorPaymentAddress is required"
/main/contracts/NFTCollection.sol#L268 "NFTCollection: Max token count has already been minted"
/main/contracts/NFTCollection.sol#L327  "NFTCollection: URI query for nonexistent token"
/main/contracts/NFTDropCollection.sol#L172 "NFTDropCollection: `count` must be greater than 0"
/main/contracts/NFTDropCollection.sol#L179 "NFTDropCollection: Exceeds max tokenId"
/main/contracts/NFTDropCollection.sol#L238 "NFTDropCollection: use `reveal` instead"
/main/contracts/mixins/collections/SequentialMintCollection.sol#L63    "SequentialMintCollection: Creator cannot be the zero address";
/main/contracts/mixins/collections/SequentialMintCollection.sol#L75    "SequentialMintCollection: Any NFTs minted must be burned first"
/main/contracts/mixins/collections/SequentialMintCollection.sol#L87    "SequentialMintCollection: Max token ID may not be cleared"
/main/contracts/mixins/collections/SequentialMintCollection.sol#L88    "SequentialMintCollection: Max token ID must be >= last mint"
/main/contracts/mixins/collections/SequentialMintCollection.sol#L89    "SequentialMintCollection: Max token ID may not increase"
```
2.  Custom Error

Custom errors can be used from Solidity 0.8.4 are cheaper than revert strings. Its cheaper deployment cost and runtime cost when the revert condition is met.

```
/main/contracts/NFTCollection.sol#L158 
/main/contracts/NFTCollection.sol#L268 
/main/contracts/NFTCollection.sol#L327  
/main/contracts/NFTDropCollection.sol#L172
/main/contracts/NFTDropCollection.sol#L179 
/main/contracts/NFTDropCollection.sol#L238
/main/contracts/mixins/collections/SequentialMintCollection.sol#L63    
/main/contracts/mixins/collections/SequentialMintCollection.sol#L75    
/main/contracts/mixins/collections/SequentialMintCollection.sol#L87    
/main/contracts/mixins/collections/SequentialMintCollection.sol#L88    
/main/contracts/mixins/collections/SequentialMintCollection.sol#L89  
```

3. change `uint256 i = 0` into `uint256 i` for saving more gas

using this implementation can saving more gas for each loops.

Files :

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L126

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L198

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L484




