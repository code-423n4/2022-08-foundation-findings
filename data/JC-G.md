
## Table of Contents
 
G-01 ++i should be unchecked{++i} (4 instances)
G-02 <array>.length should not be looked up in every loop of a for-loop (3 instances)
G-03 Using storage instead of memory for structs/arrays saves gas (4 instances)
G-04 Comparison operators (2 instances)
G-05 Using private rather than public for constants saves gas (2 instances)
G-06 calldata instead of memory for read-only function parameter (6 instances)
G-07 internal functions only called once can be inlined to save gas (8 instances)
G-08 Using > 0 costs more gas than != 0 when used on a uint in a require() statement (1 instance)
G-09 Empty blocks should be removed or emit something (3 instances)
G-10 Using bools for storage incurs overhead (1 instance)
G-11 Using storage instead of memory for structs/arrays saves gas (18 instances)
G-12 <x> += <y> costs more gas than <x> = <x> + <y> for state variables (5 instances)
G-13 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead (20 instances)
G-14 Use custom errors rather than revert()/require() strings to save gas (27 instances)
G-15 require()/revert() strings longer than 32 bytes cost extra gas (24 instances)
G-16 Functions guaranteed to revert when called by normal users can be marked payable (2 instances)
G-17 It costs more gas to initialize variables to zero than to let the default of zero be applied (5 instances)
G-18 Tight variable packing (1 instance)
G-19 Assigning keccak operations to constant variables results in extra gas costs (2 instances)
G-20 Using calldata instead of memory for read-only arguments in external functions saves gas (1 instance)

There are 138 instances in 20 gas optimizations.


## G-01 ++i should be unchecked{++i} 

when it is not possible for them to overflow, as is the case when used in for- and while-loops

There are 4 instances in 1 file:
File: contracts/mixins/shared/MarketFees.sol

    	for (uint256 i = 0; i < creatorShares.length; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L198

        for (uint256 i = 0; i < creatorRecipients.length; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L484

      	for (uint256 i = 0; i < 20; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L25

      	for (uint256 i = 0; i < 4; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L44


## G-02 <array>.length should not be looked up in every loop of a for-loop

Even memory arrays incur the overhead of bit tests and bit shifts to calculate the array length. 
Storage array length checks incur an extra Gwarmaccess (100 gas) PER-LOOP.

There are 3 instances in 1 file:
File: contracts/mixins/shared/MarketFees.sol

 	for (uint256 i = 0; i < creatorRecipients.length; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L126

    	for (uint256 i = 0; i < creatorShares.length; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L198

      	for (uint256 i = 1; i < creatorRecipients.length; ) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L503


## G-03 Using storage instead of memory for structs/arrays saves gas

There are 4 instances in 2 files:
File: contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol

   FixedPriceSaleConfig memory saleConfig = nftContractToFixedPriceSaleConfig[nftContract];
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L180

 FixedPriceSaleConfig memory saleConfig = nftContractToFixedPriceSaleConfig[nftContract];
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L284

File: contracts/libraries/BytesLibrary.sol

  bytes memory expectedData = abi.encodePacked(expectedAddress);
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L21

  bytes memory newData = abi.encodePacked(newAddress);
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L22


## G-04 Comparison operators

In the EVM, there is no opcode for >= or <=. When using greater than or equal, two operations are performed: > and =.
Using strict comparison operators hence saves gas.

There are 2 instances in 2 files:
File: contracts/NFTCollection.sol

      require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L268

File: contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol
    if (currentBalance >= limitPerAccount) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L240


## G-05 Using private rather than public for constants saves gas

If needed, the value can be read from the verified contract source code. 
Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

There are 2 instances in 2 files:
File: contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol

  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L70

File: contracts/mixins/roles/MinterRole.sol
  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/MinterRole.sol#L19


## G-06 calldata instead of memory for read-only function parameter

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. 
Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.
Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

There are 6 instances in 3 files:
File: contracts/NFTCollection.sol

    string memory _name,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L107

    string memory _symbol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L108

File: contracts/libraries/BytesLibrary.sol
    bytes memory data,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L16

  function startsWith(bytes memory callData, bytes4 functionSig) internal pure returns (bool) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L38

File: contracts/libraries/ArrayLibrary.sol

  function capLength(address payable[] memory data, uint256 maxLength) internal pure {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/ArrayLibrary.sol#L13

  function capLength(uint256[] memory data, uint256 maxLength) internal pure {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/ArrayLibrary.sol#L25


## G-07 internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

There are 8 instances in 6 files:
File: contracts/NFTDropMarket.sol

  function _getSellerOf(address nftContract, uint256 tokenId)
    internal
    view
    override(MarketSharedCore, NFTDropMarketFixedPriceSale)
    returns (address payable seller)
  {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropMarket.sol#L108-L113

 function _getSellerOrOwnerOf(address nftContract, uint256 tokenId)
    internal
    view
    override
    returns (address payable sellerOrOwner)
  {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropMarket.sol#L132-L137


File: contracts/mixins/shared/MarketFees.sol

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
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L98-L111

File: contracts/mixins/shared/MarketSharedCore.sol

  function _getSellerOrOwnerOf(address nftContract, uint256 tokenId)
    internal
    view
    virtual
    returns (address payable sellerOrOwner);
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketSharedCore.sol#L32-L36

File: contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol

 function _getSellerOf(
    address nftContract,
    uint256 /* tokenId */
  ) internal view virtual override returns (address payable seller) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L301-L304


File: contracts/libraries/BytesLibrary.sol

 function replaceAtIf(
    bytes memory data,
    uint256 startLocation,
    address expectedAddress,
    address newAddress
  ) internal pure {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L15-L20


File: contracts/libraries/AddressLibrary.sol

 function callAndReturnContractAddress(address externalContract, bytes memory callData)
    internal
    returns (address payable contractAddress)
  {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/AddressLibrary.sol#L25-L28

 function callAndReturnContractAddress(CallWithoutValue memory call)
    internal
    returns (address payable contractAddress)
  {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/AddressLibrary.sol#L34-L37



## G-08 Using > 0 costs more gas than != 0 when used on a uint in a require() statement

This change saves 6 gas per instance.

There is 1 instance in 1 file:
File: contracts/NFTDropCollection.sol

 require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L131


## G-09 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

There are 3 instances in 3 files:
File: contracts/NFTDropCollection.sol

https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L103

File: contracts/NFTCollection.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L97

File: contracts/NFTDropMarket.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropMarket.sol#L94



## G-10 Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. 
This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

There is 1 instance in 1 file:
File: contracts/mixins/shared/MarketFees.sol

  bool private immutable assumePrimarySale;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L61



## G-11 Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. 
If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. 
Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. 
The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

There are 18 instances in 1 file:
File: contracts/mixins/shared/MarketFees.sol

    address payable[] memory creatorRecipients;
    uint256[] memory creatorShares;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L112

      address payable[] memory creatorRecipients,
      uint256[] memory creatorShares,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L181

    returns (address payable[] memory recipients, uint256[] memory splitPerRecipientInBasisPoints)
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L234

        address payable[] memory _recipients,
        uint256[] memory recipientBasisPoints
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L260

  ) external view returns (address payable[] memory recipients, uint256[] memory splitPerRecipientInBasisPoints) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L283

            address payable[] memory _recipients,
            uint256[] memory recipientBasisPoints
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L317

        address payable[] memory _recipients
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L337

            uint256[] memory recipientBasisPoints
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L341

      address payable[] memory creatorRecipients,
      uint256[] memory creatorShares,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L402

      address payable[] memory _recipients,
      uint256[] memory _splitPerRecipientInBasisPoints
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L423

        address payable[] memory _recipients,
        uint256[] memory _splitPerRecipientInBasisPoints
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L435


## G-12 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

There are 5 instances in 1 file:
File: contracts/mixins/shared/MarketFees.sol
        creatorRev += creatorShares[i];
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L134

        totalFees += buyReferrerFee;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L150

    	creatorRev += creatorShares[i];
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L199

         totalShares += creatorShares[i];
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L490

        totalRoyaltiesDistributed += royalty;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L505


## G-13 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. 
Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Use a larger size then downcast where needed

There are 20 instances in 6 files:
File: contracts/NFTCollectionFactory.sol
  	uint32 public versionNFTCollection;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L80

	uint32 public versionNFTDropCollection;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L95

  	function initialize(uint32 _versionNFTCollection) external initializer {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L192

   	 uint32 maxTokenId,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L291

   	 uint32 maxTokenId,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L329

   	 uint32 maxTokenId,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L368

    	uint32 maxTokenId,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L391

File: contracts/NFTDropCollection.sol
    	uint32 _maxTokenId,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L126

  	function updateMaxTokenId(uint32 _maxTokenId) external onlyAdmin {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L220

File: contracts/NFTCollection.sol
 	function updateMaxTokenId(uint32 _maxTokenId) external onlyCreator {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L251

File: contracts/mixins/shared/Constants.sol
	uint96 constant ROYALTY_IN_BASIS_POINTS = 1000;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Constants.sol#L38

File: contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol
    uint80 price;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L54

    uint16 limitPerAccount;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L58

    uint80 price,
    uint16 limitPerAccount
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L120

    uint16 count,
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L172

File: contracts/mixins/collections/SequentialMintCollection.sol
  uint32 public latestTokenId;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L27

  uint32 public maxTokenId;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L34

  uint32 private burnCounter;
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L40

  function _initializeSequentialMintCollection(address payable _creator, uint32 _maxTokenId) internal onlyInitializing {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L62

  function _updateMaxTokenId(uint32 _maxTokenId) internal {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L86



## G-14 Use custom errors rather than revert()/require() strings to save gas

There are 27 instances in 8 files:
File: contracts/NFTCollectionFactory.sol
   	 require(rolesContract.isAdmin(msg.sender), "NFTCollectionFactory: Caller does not have the Admin role");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L173

 	 require(_rolesContract.isContract(), "NFTCollectionFactory: RolesContract is not a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L182

  	require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L203

   require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L227

    require(bytes(symbol).length != 0, "NFTCollectionFactory: Symbol is required");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L262

File: contracts/NFTDropCollection.sol
    require(bytes(_baseURI).length > 0, "NFTDropCollection: `_baseURI` must be set");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L88

    require(postRevealBaseURIHash != bytes32(0), "NFTDropCollection: Already revealed");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L93

    require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");
    require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L130

    require(count != 0, "NFTDropCollection: `count` must be greater than 0");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L172

    require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L179

    require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L238

File: contracts/NFTCollection.sol
    require(tokenCreatorPaymentAddress != address(0), "NFTCollection: tokenCreatorPaymentAddress is required");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L158

    require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");
    require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L263

      require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L268

    require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L327

File: contracts/mixins/shared/ContractFactory.so
    require(msg.sender == contractFactory, "ContractFactory: Caller is not the factory");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/ContractFactory.sol#L22

    require(_contractFactory.isContract(), "ContractFactory: Factory is not a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/ContractFactory.sol#L31

File: contracts/mixins/roles/AdminRole.sol
    require(hasRole(DEFAULT_ADMIN_ROLE, msg.sender), "AdminRole: caller does not have the Admin role");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/AdminRole.sol#L19

File: contracts/mixins/roles/MinterRole.sol
    require(isMinter(msg.sender) || isAdmin(msg.sender), "MinterRole: Must have the minter or admin role");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/MinterRole.sol#L22

File: contracts/mixins/collections/SequentialMintCollection.sol
    require(msg.sender == owner, "SequentialMintCollection: Caller is not creator");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L58

  require(_creator != address(0), "SequentialMintCollection: Creator cannot be the zero address");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L63

    require(totalSupply() == 0, "SequentialMintCollection: Any NFTs minted must be burned first");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L74

    require(_maxTokenId != 0, "SequentialMintCollection: Max token ID may not be cleared");
    require(maxTokenId == 0 || _maxTokenId < maxTokenId, "SequentialMintCollection: Max token ID may not increase");
    require(latestTokenId <= _maxTokenId, "SequentialMintCollection: Max token ID must be >= last mint");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L87

File: contracts/libraries/AddressLibrary.sol
    require(contractAddress.isContract(), "InternalProxyCall: did not return a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/AddressLibrary.sol#L31


## G-15 require()/revert() strings longer than 32 bytes cost extra gas

There 24 instances in 8 files:
File: contracts/NFTCollectionFactory.sol
   	 require(rolesContract.isAdmin(msg.sender), "NFTCollectionFactory: Caller does not have the Admin role");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L173

 	 require(_rolesContract.isContract(), "NFTCollectionFactory: RolesContract is not a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L182

  	require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L203

   require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L227

    require(bytes(symbol).length != 0, "NFTCollectionFactory: Symbol is required");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L262

File: contracts/NFTDropCollection.sol
    require(bytes(_baseURI).length > 0, "NFTDropCollection: `_baseURI` must be set");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L88

    require(postRevealBaseURIHash != bytes32(0), "NFTDropCollection: Already revealed");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L93

    require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");
    require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L130

    require(count != 0, "NFTDropCollection: `count` must be greater than 0");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L172

    require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L179

    require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTDropCollection.sol#L238

File: contracts/NFTCollection.sol
    require(tokenCreatorPaymentAddress != address(0), "NFTCollection: tokenCreatorPaymentAddress is required");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L158

    require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");
    require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L263

      require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L268

    require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollection.sol#L327

File: contracts/mixins/shared/ContractFactory.so
    require(msg.sender == contractFactory, "ContractFactory: Caller is not the factory");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/ContractFactory.sol#L22

    require(_contractFactory.isContract(), "ContractFactory: Factory is not a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/ContractFactory.sol#L31

File: contracts/mixins/roles/AdminRole.sol
    require(hasRole(DEFAULT_ADMIN_ROLE, msg.sender), "AdminRole: caller does not have the Admin role");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/AdminRole.sol#L19

File: contracts/mixins/roles/MinterRole.sol
    require(isMinter(msg.sender) || isAdmin(msg.sender), "MinterRole: Must have the minter or admin role");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/MinterRole.sol#L22

File: contracts/mixins/collections/SequentialMintCollection.sol
    require(msg.sender == owner, "SequentialMintCollection: Caller is not creator");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L58

  require(_creator != address(0), "SequentialMintCollection: Creator cannot be the zero address");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L63

    require(totalSupply() == 0, "SequentialMintCollection: Any NFTs minted must be burned first");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L74

    require(_maxTokenId != 0, "SequentialMintCollection: Max token ID may not be cleared");
    require(maxTokenId == 0 || _maxTokenId < maxTokenId, "SequentialMintCollection: Max token ID may not increase");
    require(latestTokenId <= _maxTokenId, "SequentialMintCollection: Max token ID must be >= last mint");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/SequentialMintCollection.sol#L87

File: contracts/libraries/AddressLibrary.sol
    require(contractAddress.isContract(), "InternalProxyCall: did not return a contract");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/AddressLibrary.sol#L31


## G-16 Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. 
Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 
The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), 
which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There 2 instances in 1 file: 
File: contracts/NFTCollectionFactory.sol
  function adminUpdateNFTCollectionImplementation(address _implementation) external onlyAdmin {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L202

  function adminUpdateNFTDropCollectionImplementation(address _implementation) external onlyAdmin {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L226


## G-17 It costs more gas to initialize variables to zero than to let the default of zero be applied

There 5 instances in 2 files: 
File: contracts/mixins/shared/MarketFees.sol
      for (uint256 i = 0; i < creatorRecipients.length; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L126

    for (uint256 i = 0; i < creatorShares.length; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L198

         for (uint256 i = 0; i < creatorRecipients.length; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L484

File:contracts/libraries/BytesLibrary.sol
      for (uint256 i = 0; i < 20; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L25

      for (uint256 i = 0; i < 4; ++i) {
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L44


## G-18 Tight variable packing

Solidity contracts have contiguous 32 bytes (256 bits) slots used in storage. By arranging the variables, it is possible to minimize the number of slots used within a contract’s storage and therefore reduce deployment costs.
address type variables are each of 20 bytes size (way less than 32 bytes). However, they here take up a whole 32 bytes slot (they are contiguous).
As bool type variables are of size 1 byte, there’s a slot here that can get saved by moving them closer to an address

There is 1 instance in 1 file:
File: contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol
      address payable seller,
      uint256 price,
      uint256 limitPerAccount,
      uint256 numberOfTokensAvailableToMint,
      bool marketCanMint
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L268


## G-19 Assigning keccak operations to constant variables results in extra gas costs

In a number of places a keccak("string") expression is assigned to a constant variable. 
Due to how constant variables are implemented this results in the hash being recomputed each time that the variable is used, spending the gas necessary to perform this action.
If these variables were to be immutable this hash is calculated once at deploy time and then the result is saved to be used directly at runtime rather than recalculating, saving the cost of hashing.

Instances:There 2 instances in 2 files:
File: contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol
  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L70

File: contracts/mixins/roles/MinterRole.sol
  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/MinterRole.sol#L19

Recommended Mitigation Step: Change all constant hashes to be immutable


## G-20 Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. 
Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). 
Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one.

There is 1 instance in 1 file:
File: contracts/NFTCollectionFactory.sol
    CallWithoutValue memory paymentAddressFactoryCall
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L371

