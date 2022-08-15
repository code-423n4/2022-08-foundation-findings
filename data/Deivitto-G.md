# GAS
## Constants expressions are expressions, not constants.
### summary
Constant expressions are left as expressions, not constants.
### details
Reference to this kind of issue: https://github.com/ethereum/solidity/issues/9232
### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L70
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/MinterRole.sol#L19
### mitigation
Use immutable for this expressions and set it on constructor



## use of custom errors rather than revert() / require() error message
### summary
Custom errors reduce 38 gas if the condition is met and 22 gas otherwise.
Also reduces contract size and deployment costs.
### details
Since version 0.8.4 the use of custom errors rather than revert() / require() saves gas as noticed in
https://blog.soliditylang.org/2021/04/21/custom-errors/
https://github.com/code-423n4/2022-04-pooltogether-findings/issues/13

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L158
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L263
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L264
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L268
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L327

https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L173
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L182
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L203
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L227
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L262

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L88
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L93
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L130
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L131
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L172
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L179
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L238

https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/AddressLibrary.sol#L31

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L58
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L63
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L74
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L87
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L88
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L89

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/AdminRole.sol#L19

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/MinterRole.sol#L22

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/ContractFactory.sol#L22
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/ContractFactory.sol#L31

### mitigation
replace each error message in a require by a custom error

## duplicated require() check should be refactored
### summary
duplicated require() / revert() checks should be
refactored to a modifier or function to save gas
### details
Event appears twice and can be reduced

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L203
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L227

### mitigation
refactor this checks to different functions to save gas

## use != rather than >0 for unsigned integers in require() statements
### Summary
When the optimizer is enabled, gas is wasted by doing a greater-than operation, rather than a not-equals operation inside require() statements. When Using != , the optimizer is able to avoid the EQ, ISZERO, and associated operations, by relying on the JUMPI that comes afterwards, which itself checks for zero.
### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L88
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L130
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L131
### mitigation
Use != 0 rather than > 0 for unsigned integers in require()  statements.



## Reduce the size of error messages (Long revert Strings)
### summary
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L158
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L263
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L264
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L268
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L327

https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L173
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L182
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L203
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L227
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L262

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L88
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L93
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L130
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L131
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L172
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L179
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L238

https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/AddressLibrary.sol#L31

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L58
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L63
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L74
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L87
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L88
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L89

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/AdminRole.sol#L19

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/MinterRole.sol#L22


https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/ContractFactory.sol#L22
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/ContractFactory.sol#L31

### mitigation
Consider shortening the revert strings to fit in 32 bytes


## Using bools for storage incurs overhead { 
### summary
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

### details
Here is one example of OpenZeppelin about this optimization
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L53
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L61
### mitigation
Consider using uint256 with values 0 and 1 rather than bool





## Store using Struct over multiple mappings
### summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L55-L64

### mitigation
Consider mixing different mappings into an struct when able in order to save gas.


## Unused named returns
### summary
Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity 
### details
Also as returns variable is ignored, it wastes extra gas

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L286-L306
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L324-L345
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L363-L384
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L300-L304
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropMarket.sol#L108-L126
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropMarket.sol#L132-L150
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/AddressLibrary.sol#L34-L39
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L227-L252
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L264-L295
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/mixins/shared/FoundationTreasuryNode.sol#L59-L61
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L208-L210
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L217-L223
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L279-L383
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketSharedCore.sol#L19-L36

### mitigation
Remove return or returns when both used


## Use calldata instead of memory for function parameters
### Summary
It is generally cheaper to load variables directly from calldata, rather than copying them to memory. 
### Details
Only use memory if the variable needs to be modified
### Github Permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L107-L108
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L371

### Mitigation
Use calldata rather than memory in external functions where the parameter is not modified but only read

## Using private rather than public for constants saves gas
### summary
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L70
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/MinterRole.sol#L19
### mitigation
Consider replacing public for private in constants for gas saving.

## Explicit initialization
### summary
It is not needed to initialize variables to the default value. Explicitly initializing it with its default value is an anti-pattern and wastes gas.
### details
If a variable is not set/initialized, it is assumed to have the default value ( 0 for uint, false for bool, address(0) for address…). 

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/mixins/shared/Constants.sol#L15

### mitigation
Don't initialize variables to default value

## Index initialized in for loop
### summary
In for loops is not needed to initialize indexes to 0 as it is the default uint/int value. This saves gas.

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/BytesLibrary.sol#L25
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/BytesLibrary.sol#L44
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L126
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L198
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L484

### mitigation
Don't initialize variables to default value



## ++i costs less gas compared to i++
### summary
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). 
This statement is true even with the optimizer enabled. 

### details
i++ increments i and returns the initial value of i. 
Which means:
uint i = 1;
i++; // == 1 but i == 2

But ++i returns the actual incremented value:

uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

### github permalinks
var++
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L207
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L231

### mitigation
Replace to ++i.


## increments can be unchecked in loops
### summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:

    for (uint256 i; i < numIterations; i++) {
    // ...
    }

    to

    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L198-L200

### mitigation
Add unchecked ++i at the end of all the for loop where it's not expected to overflow and remove them from the for header


## <array>.length should no be looked up in every loop of a for-loop
### summary
In loops not assigning the length to a variable so memory accessed a lot (caching local variables)

### details
The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

### github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L126
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L198
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L484
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L503

### mitigation
Assign the length of the array.length to a local variable in loops for gas savings


## Variables should be cached when used several times
### summary
Variables read more than once improves gas usage when cached into local variable
### details
NOTE: In loops or state variables, this is even more gas saving

### github permalinks
latestTokenId
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L181

creatorShares[i]
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L129-L134
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L485-L490
### mitigation
Cache variables used more than one into a local variable.



## >= cheaper than >
### Summary
Strict inequalities ( > ) are more expensive than non-strict ones ( >= ). This is due to some supplementary checks (ISZERO, 3 gas)

### Github permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L88
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L130
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L131
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L244

### mitigation
Consider using >= 1 instead of > 0 to avoid some opcodes


