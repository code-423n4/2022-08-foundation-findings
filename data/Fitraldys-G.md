1. Use Custom Error instead of Revert / Require String to Save Gas

Custom error from solidity 0.8.4 are cheaper than revert strings, custom error are defined using the `error` statement can use inside and outside the contract.

source https://blog.soliditylang.org/2021/04/21/custom-errors/

i suggest replacing revert / require error strings with custom error.

POC :

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L173
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L203
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L262
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L88
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L93
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L130
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L131
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L172
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L179
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L238
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L158
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L263
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L264
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L268
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L327

2. `require()`/`revert()` strings longer than 32 bytes cost extra gas

Each extra chunk of bytes past the original 32 which costs 3 gas.

resource : https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings

POC :

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L227
