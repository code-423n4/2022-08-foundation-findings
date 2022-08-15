1)It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {  

File: 2022-08-foundation\contracts\libraries\BytesLibrary.sol
  25,20:       for (uint256 i = 0; i < 20; ++i) {
  44,20:       for (uint256 i = 0; i < 4; ++i) {
  
File: 2022-08-foundation\contracts\mixins\shared\MarketFees.sol
  126,20:       for (uint256 i = 0; i < creatorRecipients.length; ++i) {
  198,18:     for (uint256 i = 0; i < creatorShares.length; ++i) {
  484,24:           for (uint256 i = 0; i < creatorRecipients.length; ++i) { 

File: 2022-08-foundation\contracts\libraries\BytesLibrary.sol
  25,7:       for (uint256 i = 0; i < 20; ++i) {
  44,7:       for (uint256 i = 0; i < 4; ++i) {    

2)  <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset


File: 2022-08-foundation\contracts\mixins\shared\MarketFees.sol
  126,7:       for (uint256 i = 0; i < creatorRecipients.length; ++i) {
  198,5:     for (uint256 i = 0; i < creatorShares.length; ++i) {
  484,11:           for (uint256 i = 0; i < creatorRecipients.length; ++i) {
  503,7:       for (uint256 i = 1; i < creatorRecipients.length; ) {  
  
3)++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{++I} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS  

File: 2022-08-foundation\contracts\libraries\BytesLibrary.sol
  25,20:       for (uint256 i = 0; i < 20; ++i) {
  44,20:       for (uint256 i = 0; i < 4; ++i) {
  
File: 2022-08-foundation\contracts\mixins\shared\MarketFees.sol
  126,20:       for (uint256 i = 0; i < creatorRecipients.length; ++i) {
  198,18:     for (uint256 i = 0; i < creatorShares.length; ++i) {
  484,24:           for (uint256 i = 0; i < creatorRecipients.length; ++i) { 

File: 2022-08-foundation\contracts\libraries\BytesLibrary.sol
  25,7:       for (uint256 i = 0; i < 20; ++i) {
  44,7:       for (uint256 i = 0; i < 4; ++i) {  

4)Use custom errors rather than revert()/require() strings to save gas

File: 2022-08-foundation\contracts\NFTCollection.sol
  158,5:     require(tokenCreatorPaymentAddress != address(0), "NFTCollection: tokenCreatorPaymentAddress is required");
  263,5:     require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");
  264,5:     require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");
  268,7:       require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
  327,5:     require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");
  
File: 2022-08-foundation\contracts\NFTCollectionFactory.sol
  173,5:     require(rolesContract.isAdmin(msg.sender), "NFTCollectionFactory: Caller does not have the Admin role");
  182,5:     require(_rolesContract.isContract(), "NFTCollectionFactory: RolesContract is not a contract");
  203,5:     require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
  227,5:     require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
  262,5:     require(bytes(symbol).length != 0, "NFTCollectionFactory: Symbol is required");


File: 2022-08-foundation\contracts\NFTDropCollection.sol
  88,5:     require(bytes(_baseURI).length > 0, "NFTDropCollection: `_baseURI` must be set");
  93,5:     require(postRevealBaseURIHash != bytes32(0), "NFTDropCollection: Already revealed");
  130,5:     require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");
  131,5:     require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
  172,5:     require(count != 0, "NFTDropCollection: `count` must be greater than 0");
  179,5:     require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");
  238,5:     require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");


File: 2022-08-foundation\contracts\libraries\AddressLibrary.sol
  31,5:     require(contractAddress.isContract(), "InternalProxyCall: did not return a contract");  
  
File: 2022-08-foundation\contracts\mixins\collections\SequentialMintCollection.sol
  58,5:     require(msg.sender == owner, "SequentialMintCollection: Caller is not creator");
  63,5:     require(_creator != address(0), "SequentialMintCollection: Creator cannot be the zero address");
  74,5:     require(totalSupply() == 0, "SequentialMintCollection: Any NFTs minted must be burned first");
  87,5:     require(_maxTokenId != 0, "SequentialMintCollection: Max token ID may not be cleared");
  88,5:     require(maxTokenId == 0 || _maxTokenId < maxTokenId, "SequentialMintCollection: Max token ID may not increase");
  89,5:     require(latestTokenId <= _maxTokenId, "SequentialMintCollection: Max token ID must be >= last mint");

File: 2022-08-foundation\contracts\mixins\roles\AdminRole.sol
  19,5:     require(hasRole(DEFAULT_ADMIN_ROLE, msg.sender), "AdminRole: caller does not have the Admin role");

File: 2022-08-foundation\contracts\mixins\roles\MinterRole.sol
  22,5:     require(isMinter(msg.sender) || isAdmin(msg.sender), "MinterRole: Must have the minter or admin role");  
  
File: 2022-08-foundation\contracts\mixins\shared\ContractFactory.sol
  22,5:     require(msg.sender == contractFactory, "ContractFactory: Caller is not the factory");
  31,5:     require(_contractFactory.isContract(), "ContractFactory: Factory is not a contract");  
  
File: 2022-08-foundation\contracts\mixins\treasury\AdminRole.sol
  20,5:     require(hasRole(DEFAULT_ADMIN_ROLE, msg.sender), "AdminRole: caller does not have the Admin role");  
  
 
 
4)X = X + Y IS CHEAPER THAN X += Y 
File: 2022-08-foundation\contracts\mixins\shared\MarketFees.sol
  134,20:         creatorRev += creatorShares[i];
  150,19:         totalFees += buyReferrerFee;
  199,18:       creatorRev += creatorShares[i];
  490,25:             totalShares += creatorShares[i];
  505,35:         totalRoyaltiesDistributed += royalty;

5)Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code.
Savings are due to the compiler not having to create non-payable getter
functions for deployment calldata, and not adding another entry to the method ID table  
  
File: 2022-08-foundation\contracts\mixins\nftDropMarket\NFTDropMarketFixedPriceSale.sol
  70,11:   bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");  

File: 2022-08-foundation\contracts\mixins\roles\MinterRole.sol
  19,11:   bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE"); 

6)USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT IN A REQUIRE() STATEMENT

This change saves 6 gas per instance

  
File: 2022-08-foundation\contracts\NFTDropCollection.sol
  88,36:     require(bytes(_baseURI).length > 0, "NFTDropCollection: `_baseURI` must be set");
  130,35:     require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");
  131,25:     require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");    


  