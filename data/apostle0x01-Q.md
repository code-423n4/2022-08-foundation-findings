## [L-01] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
contracts/FETH.sol::42 => pragma solidity ^0.8.12;
contracts/FoundationTreasury.sol::48 => pragma solidity ^0.8.12;
contracts/NFTCollection.sol::3 => pragma solidity ^0.8.12;
contracts/NFTCollectionFactory.sol::42 => pragma solidity ^0.8.12;
contracts/NFTDropCollection.sol::3 => pragma solidity ^0.8.12;
contracts/NFTDropMarket.sol::42 => pragma solidity ^0.8.12;
contracts/mixins/collections/CollectionRoyalties.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/collections/SequentialMintCollection.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/nftDropMarket/NFTDropMarketCore.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/roles/AdminRole.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/roles/MinterRole.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/shared/Constants.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/shared/ContractFactory.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/shared/FETHNode.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/shared/FoundationTreasuryNode.sol::3 => pragma solidity ^0.8.12;
contracts/mixins/shared/Gap10000.sol::3 => pragma solidity ^0.8.12;
```


## [L-02] Do not use Deprecated Library Functions

#### Impact
The usage of deprecated library functions should be discouraged.
This issue is mostly related to OpenZeppelin libraries.

#### Findings:
```
contracts/mixins/treasury/AdminRole.sol::16 => _setupRole(DEFAULT_ADMIN_ROLE, admin);
```

As stated here:https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3488    ,  `_setupRole` has been deprecated in favor of `_grantRole`
