# [L-01] Use _safeMint() instead of mint()

[OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC721/ERC721Upgradeable.sol#L275) recommendation is to use `_safeMint()` instead of `mint()` for ERC721Upgradeable.

`_safeMint()` will ensure that the recipient implements IERC721Receiver or is an EOA.

```
File: contracts/NFTDropCollection.sol
182: _mint(to, i);
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol

# [L-02] Missing constructor and initializer modifier for contracts using Initializable

## Impact

OpenZeppelin [recommends](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/6) adding an empty `constructor ` with the `initializer` modifier in order to avoid potential griefs, social engineering and other exploits.

```
File: contracts/NFTDropCollection.sol
62: contract NFTCollectionFactory is ICollectionFactory, Initializable, Gap10000 {
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol

```
File: contracts/NFTDropCollection.sol
28: contract NFTDropCollection is
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol

```
File: contracts/NFTDropMarket.sol
63: contract NFTDropMarket is
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropMarket.sol

```
File: contracts/mixins/collections/SequentialMintCollection.sol
13: abstract contract SequentialMintCollection is ITokenCreator, Initializable, ERC721BurnableUpgradeable {
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol

```
File: contracts/mixins/roles/AdminRole.sol
12: abstract contract AdminRole is Initializable, AccessControlUpgradeable {
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/AdminRole.sol

```
File: contracts/mixins/treasury/AdminRole.sol
13: abstract contract AdminRole is Initializable, OZAccessControlUpgradeable {
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/treasury/AdminRole.sol

```
File: contracts/mixins/roles/MinterRole.sol
14: abstract contract MinterRole is Initializable, AccessControlUpgradeable, AdminRole {
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/MinterRole.sol

## Recommended Mitigations Steps

Ensure that the modifier is applied to the implementation contract. If the default constructor is currently being used, it should be changed to be an explicit one with the modifier applied.

## [NC-01] Lock the pragma version

Even if the idea is to make use of the latests versions of solidity, I would still recommend to [lock the pragma](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/) for contracts that will be deployed to the mainnet and are not intended to be consumed by other developers.

All the 20 contracts in scope are rounding the pragma version.

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropMarket.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/AddressLibrary.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/ArrayLibrary.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/CollectionRoyalties.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketCore.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/AdminRole.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/MinterRole.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Constants.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/ContractFactory.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/FETHNode.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/FoundationTreasuryNode.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketSharedCore.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/treasury/AdminRole.sol

# [NC-02] Replace public with external for functions not called by the contract internally

```
File: contracts/NFTDropCollection.sol
159: function burn(uint256 tokenId) public override onlyAdmin {
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol

```
File: contracts/mixins/shared/FoundationTreasuryNode.sol
59: function getFoundationTreasury() public view returns (address payable treasuryAddress) {
```

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/FoundationTreasuryNode.sol
