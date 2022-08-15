## Consider using custom errors instead of revert strings

This reduce gas cost as show here https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5

`Solidity 0.8.4 introduced custom errors. They are more gas efficient than revert strings, when it comes to deployment cost as well as runtime cost when the revert condition is met. Use custom errors instead of revert strings for gas savings.`

Any require statement in your code can be replaced with custom error for example,

https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L238

```
require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");
```

Can be replaced with

```
// declare error before contract declaration
error UseRevealInstead();

if (_postRevealBaseURIHash == bytes32(0)) revert UseRevealInstead();
```