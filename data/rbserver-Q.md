## [L-01] PROTOCOL AND BUY REFERRER FEES CAN BE 0 WHEN CALLING createFixedPriceSale FUNCTION WITH LOW price INPUT AND WHEN CALLING mintFromFixedPriceSale FUNCTION WITH LOW count INPUT 

When calling the `createFixedPriceSale` function for creating a fixed price sale drop, the `price` input can be set as low as 0, which is also confirmed by the following code comment.

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L118-L157
```
  function createFixedPriceSale(
    address nftContract,
    uint80 price,
    uint16 limitPerAccount
  ) external {
    ...

    // Any price is supported, including 0.

    ...
  }
```

When the `mintFromFixedPriceSale` function is called, `mintCost`, which is `uint256(saleConfig.price) * count`, is used for calling the `_distributeFunds` function as shown below.

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L170-L219
```
  function mintFromFixedPriceSale(
    address nftContract,
    uint16 count,
    address payable buyReferrer
  ) external payable returns (uint256 firstTokenId) {
    ...

    // Calculate the total cost, considering the `count` requested.
    uint256 mintCost;
    unchecked {
      // Can not overflow as 2^80 * 2^16 == 2^96 max which fits in 256 bits.
      mintCost = uint256(saleConfig.price) * count;
    }

    ...

    // Distribute revenue from this sale.
    (uint256 totalFees, uint256 creatorRev, ) = _distributeFunds(
      nftContract,
      firstTokenId,
      saleConfig.seller,
      mintCost,
      buyReferrer
    );

    ...
  }
```

When the `_distributeFunds` function is called, `mintCost` is further used as the `price` input for calling the `_getFees` function.

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L98-L153
```
  function _distributeFunds(
    address nftContract,
    uint256 tokenId,
    address payable seller,
    uint256 price,
    address payable buyReferrer
  )
    internal
    returns (
      uint256 totalFees,
      uint256 creatorRev,
      uint256 sellerRev
    )
  {
    address payable[] memory creatorRecipients;
    uint256[] memory creatorShares;

    uint256 buyReferrerFee;
    (totalFees, creatorRecipients, creatorShares, sellerRev, buyReferrerFee) = _getFees(
      nftContract,
      tokenId,
      seller,
      price,
      buyReferrer
    );

    ...

    // Pay the protocol fee
    _sendValueWithFallbackWithdraw(getFoundationTreasury(), totalFees, SEND_VALUE_GAS_LIMIT_SINGLE_RECIPIENT);

    // Pay the buy referrer fee
    if (buyReferrerFee != 0) {
      _sendValueWithFallbackWithdraw(buyReferrer, buyReferrerFee, SEND_VALUE_GAS_LIMIT_SINGLE_RECIPIENT);
      emit BuyReferralPaid(nftContract, tokenId, buyReferrer, buyReferrerFee, 0);
      unchecked {
        // Add the referrer fee back into the total fees so that all 3 return fields sum to the total price for events
        totalFees += buyReferrerFee;
      }
    }
  }
```

In the `_getFees` function, `mintCost` is used as `price` for calculating `totalFees = price / PROTOCOL_FEE_DENOMINATOR`. If `mintCost` is smaller than `PROTOCOL_FEE_DENOMINATOR`, `totalFees` will be 0 per Solidity's behavior. This also means that `buyReferrerFee` is 0 because `buyReferrerFee = totalFees / BUY_REFERRER_PROTOCOL_FEE_DENOMINATOR`.

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L391-L530
```
  function _getFees(
    address nftContract,
    uint256 tokenId,
    address payable seller,
    uint256 price,
    address payable buyReferrer
  )
    private
    view
    returns (
      uint256 totalFees,
      address payable[] memory creatorRecipients,
      uint256[] memory creatorShares,
      uint256 sellerRev,
      uint256 buyReferrerFee
    )
  {
    // Calculate the protocol fee
    unchecked {
      // SafeMath is not required when dividing by a non-zero constant.
      totalFees = price / PROTOCOL_FEE_DENOMINATOR;
    }
	
    ...

    if (buyReferrer != address(0) && buyReferrer != msg.sender && buyReferrer != seller && buyReferrer != creator) {
      unchecked {
        buyReferrerFee = totalFees / BUY_REFERRER_PROTOCOL_FEE_DENOMINATOR;

        // buyReferrerFee is always <= totalFees
        totalFees -= buyReferrerFee;
      }
    }
  }
```

`mintCost` can be small if `price` per NFT is set to a low value, including 0, when calling the `createFixedPriceSale` function and `count` is also set to a low value when calling the `mintFromFixedPriceSale` function. This would lead to a case where the protocol and buyer referrer fees are 0. If this is as expected, the documentation can be updated to at least indicate that buyer referrer will receive no fees in this situation. Otherwise, please consider implementing minimum fees for the protocol and buyer referrer.

## [L-02] \_symbol INPUT CAN BE CHECKED FOR initialize FUNCTION OF NFTCollection CONTRACT
For the `initialize` function of the `NFTCollection` contract, the `_symbol` input is not further checked. In the `NFTDropCollection` contract, `require(bytes(_symbol).length > 0, "NFTDropCollection: '_symbol' must be set");` is executed in the `initialize` function. To ensure that `_symbol` is set as expected, a similar `require` statement can be added for the `NFTCollection` contract's `initialize` function.

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L105-L112
```
  function initialize(
    address payable _creator,
    string memory _name,
    string memory _symbol
  ) external initializer onlyContractFactory {
    __ERC721_init(_name, _symbol);
    _initializeSequentialMintCollection(_creator, 0);
  }
```

## [L-03] UNRESOLVED TODO COMMENT
Comment regarding todo indicates that there is an unresolved action item for implementation, which need to be addressed before protocol deployment.

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L188-L195
```
    (totalFees, creatorRecipients, creatorShares, sellerRev, ) = _getFees(
      nftContract,
      tokenId,
      seller,
      price,
      // TODO add referral info
      payable(0)
    );
```

## [N-01] REDUNDANT NAMED RETURNS
When a function has unused named returns and used return statement, these named returns become redundant. To improve readability and maintainability, these variables for the named returns can be removed while keeping the return statements for the functions associated with the following lines.
```
contracts\NFTCollectionFactory.sol
  294: ) external returns (address collection) {
  333: ) external returns (address collection) {
  372: ) external returns (address collection) {
```

## [N-02] UNUSED IMPORTS
The following imports are not used. Please consider removing them for better readability and maintainability.
```
contracts\NFTDropCollection.sol
  20: import "./mixins/shared/Constants.sol";

contracts\NFTDropMarket.sol
  47: import "./mixins/shared/Constants.sol";
```

## [N-03] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.
```
contracts\NFTCollection.sol
  262: function _mint(string calldata tokenCID) private onlyCreator returns (uint256 tokenId) {

contracts\NFTCollectionFactory.sol
  386: function _createNFTDropCollection(
  448: function _getSalt(address creator, uint256 nonce) private pure returns (bytes32) { 

contracts\libraries\AddressLibrary.sol
  34: function callAndReturnContractAddress(CallWithoutValue memory call)

contracts\mixins\collections\SequentialMintCollection.sol
  62: function _initializeSequentialMintCollection(address payable _creator, uint32 _maxTokenId) internal onlyInitializing { 
```