## QA

## Missing checks for address(0x0) when assigning values to address state variables
File: NFTCollectionFactory.sol [Line 184](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L184)(Investigate this)
```solidity
    rolesContract = IRoles(_rolesContract);
```

## Unused named return
Using both named returns and a return statement isn’t necessary in  a function.To  improve code quality, consider using only one of those.

File: NFTDropMarketFixedPriceSale.sol [Line 227-252](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L227-L252)
```solidity
  function getAvailableCountFromFixedPriceSale(address nftContract, address user)
    external
    view
    returns (uint256 numberThatCanBeMinted)
  {
    (, , uint256 limitPerAccount, uint256 numberOfTokensAvailableToMint, bool marketCanMint) = getFixedPriceSale(
      nftContract
    );
    if (!marketCanMint) {
      // No one can mint in the current state.
      return 0;
    }
    uint256 currentBalance = IERC721(nftContract).balanceOf(user);
    if (currentBalance >= limitPerAccount) {
      // User has exhausted their limit.
      return 0;
    }


    uint256 availableToMint = limitPerAccount - currentBalance;
    if (availableToMint > numberOfTokensAvailableToMint) {
      // User has more tokens available than the collection has available.
      return numberOfTokensAvailableToMint;
    }


    return availableToMint;
  }
 ```

File:FoundationTreasuryNode.sol [Line 59-61](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/FoundationTreasuryNode.sol#L59-L61)
```solidity
  function getFoundationTreasury() public view returns (address payable treasuryAddress) {
    return treasury;
  }
```
The above returns **treasury** instead of the named **treasuryAddress**


File:MarketFees.sol [Line 208-210](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L203-L210)
```solidity
  /**
   * @notice Returns the address of the registry allowing for royalty configuration overrides.
   * @dev See https://royaltyregistry.xyz/
   * @return registry The address of the royalty registry contract.
   */
  function getRoyaltyRegistry() external view returns (address registry) {
    return address(royaltyRegistry);
  }
```
The above returns **address(royaltyRegistry)** instead of the named **registry** 

File: NFTDropCollection.sol [Line 300-304](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L300-L304)
```solidity
  function tokenURI(uint256 tokenId) public view override returns (string memory uri) {
    _requireMinted(tokenId);

    return string.concat(baseURI, tokenId.toString(), ".json");
  }
```

## Typos/Grammer
File: NFTCollection.sol [Line 189](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L189)
```solidity
   * @param paymentAddressCallData The call details to sent to the factory provided.
```
**sent** should be **send**

File: NFTCollection.sol [Line 211](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L211)
```solidity
   * @param paymentAddressCallData The call details to sent to the factory provided.
```
**sent** should be **send**

File: SequentialMintCollection.sol [Line 17](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L17)
```solidity
   * @dev This is the default royalty recipient if an different `paymentAddress` was not provided.
```
**an** should be **a**

## Open todo

File: MarketFees.sol [Line 193](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L193)
```solidity
      // TODO add referral info
```

## Lack of event emission after critical initialize() functions

File: NFTCollectionFactory.sol [Line 192-194](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L192-L194)
```solidity
  function initialize(uint32 _versionNFTCollection) external initializer {
    versionNFTCollection = _versionNFTCollection;
  }
```

File: NFTDropCollection.sol [Line 120](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L120)
```solidity
  function initialize(
```

File: NFTCollection.sol [Line 105-112](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L105-L112)
```solidity
  function initialize(
    address payable _creator,
    string memory _name,
    string memory _symbol
  ) external initializer onlyContractFactory {
    __ERC721_init(_name, _symbol);
    _initializeSequentialMintCollection(_creator, 0);
  }
```

File: NFTDropMarket.sol [Line 100-102](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropMarket.sol#L100-L102)
```solidity
  function initialize() external initializer {
    ReentrancyGuardUpgradeable.__ReentrancyGuard_init();
  }
```


## Emit after all processing is done
File: MarketFees.sol [Line 147](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L147)
```solidity
    if (buyReferrerFee != 0) {
      _sendValueWithFallbackWithdraw(buyReferrer, buyReferrerFee, SEND_VALUE_GAS_LIMIT_SINGLE_RECIPIENT);
      emit BuyReferralPaid(nftContract, tokenId, buyReferrer, buyReferrerFee, 0);
      unchecked {
        // Add the referrer fee back into the total fees so that all 3 return fields sum to the total price for events
        totalFees += buyReferrerFee;
      }
    }
```

## public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.

File: NFTDropCollection.sol [Line 252-259](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L252-L259)
```solidity
  function getTokenCreatorPaymentAddress(
    uint256 /* tokenId */
  ) public view override returns (address payable creatorPaymentAddress) {
    creatorPaymentAddress = paymentAddress;
    if (creatorPaymentAddress == address(0)) {
      creatorPaymentAddress = owner;
    }
  }
```

File: NFTDropCollection.sol [Line 300-304](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L300-L304)
```solidity
  function tokenURI(uint256 tokenId) public view override returns (string memory uri) {
    _requireMinted(tokenId);

    return string.concat(baseURI, tokenId.toString(), ".json");
  }
```

File: NFTCollection.sol [Line 298-308](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L298-L308)

```solidity
  function getTokenCreatorPaymentAddress(uint256 tokenId)
    public
    view
    override
    returns (address payable creatorPaymentAddress)
  {
    creatorPaymentAddress = tokenIdToCreatorPaymentAddress[tokenId];
    if (creatorPaymentAddress == address(0)) {
      creatorPaymentAddress = owner;
    }
  }
```

File: NFTCollection.sol [Line 326-330](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L326-L330)
```solidity
  function tokenURI(uint256 tokenId) public view override returns (string memory uri) {
    require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");


    uri = string.concat(_baseURI(), _tokenCIDs[tokenId]);
  }
```

File: AdminRole.sol [Line 47-49](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/roles/AdminRole.sol#L47-L49)
```solidity
  function isAdmin(address account) public view returns (bool approved) {
    approved = hasRole(DEFAULT_ADMIN_ROLE, account);
  }
```

File:FoundationTreasuryNode.sol [Line 59-61](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/FoundationTreasuryNode.sol#L59-L61)
```solidity
  function getFoundationTreasury() public view returns (address payable treasuryAddress) {
    return treasury;
  }
```
