# Summary:
Overall, the code base was quite well written in my opinion and had great test coverage. The MarketFees mixin logic was a bit trickier to follow if I were to give any constructive feedback.

# Issue 1:
OpenZeppelin recommends the usage of _safeMint() instead of _mint() since safeMint() checks whether a contract can handle ERC721 tokens.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/2dc086563f2e51620ebc43d2237fc10ef201c4e6/contracts/token/ERC721/ERC721.sol#L270

If the user provides an address that can't handle ERC721 tokens when calling any of the corresponding `mint` functions the minted token might be lost which could potentially result in the user not being able to redeem the nft anymore.

In several places:

https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L130
https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L143
https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L159
https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L271
https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L182

This was previously reported and confirmed by HardlyDifficult (sponsor) and has also been reported in C4 before.
https://github.com/code-423n4/2022-02-foundation-findings/issues/50
https://github.com/code-423n4/2021-11-vader-findings/issues/27


# Issue 2:
Inconsistent check on requiring a symbol to be passed when creating a collection, confirmed by Hardly Difficult (sponsor).

There is a check here https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L262 but no check here for example: https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L295. The check does not happen until inside `initialize`: https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L130

Would recommend consistency on the require check for better readability/developer experience.



