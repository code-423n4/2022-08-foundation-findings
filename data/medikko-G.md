### Intializing the variables to zero that aren't `constant` or `immutable` will cost more gas rather than use default value of zero.

If you not overwritte the default value you will save 8 gas for stack variables and more for storage and memory variables.

_There are **8** instances of this issue:_

```solidity
File: /contracts/PercentSplitETH.sol:

115: for (uint256 i = 0; i < _shares.length; ++i) {

135: for (uint256 i = 0; i < shares.length; ++i) {

320: for (uint256 i = 0; i < shares.length; ++i) {
```

```solidity
File: /contracts/libraries/BytesLibrary.sol

25:     for (uint256 i = 0; i < 20; ++i) {

44:     for (uint256 i = 0; i < 4; ++i) {
```

```solidity
File: /contracts/mixins/shared/MarketFees.sol

126:    for (uint256 i = 0; i < creatorRecipients.length; ++i) {

198:    for (uint256 i = 0; i < creatorShares.length; ++i) {

484:    for (uint256 i = 0; i < creatorRecipients.length; ++i) {
```

### Use of  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}`in `for`-loops

Not using a `unchecked{++i}`/`unchecked{i++}` will cost more gas because the default compiler overflow and underflow safety checks. This is true from version 0.8.0, code below match that requirments.

_There are **2** instances of this issue:_

```solidity
File: /contracts/PercentSplitETH.sol:

115: for (uint256 i = 0; i < _shares.length; ++i) {

320: for (uint256 i = 0; i < shares.length; ++i) {
```


### If you use bit shifting will save some gas

Use of bit shifting operations are more cheap than normal multiplication/division operations.

_There are **3** instances of this issue:_

```
File: /contracts/FETH.sol

210:    lockupInterval = _lockupDuration / 24;

211:    if (lockupInterval * 24 != _lockupDuration || _lockupDuration == 0) {
```

```solidity
File: ./contracts/libraries/LockedBalance.sol

100: uint256 lockupMetadata = lockups.lockups[index / 2];
```