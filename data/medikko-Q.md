### Unsafe ERC20 Operation(s)

RC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

_There are **2** instances of this issue:_

```solidity
File: contracts/PercentSplitETH.sol

236:    try erc20Contract.transfer(share.recipient, amountToSend) {
245:    try erc20Contract.transfer(_shares[0].recipient, amountToSend) {
```
