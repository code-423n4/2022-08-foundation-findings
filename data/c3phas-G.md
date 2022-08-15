## FINDINGS
## The result of a function call should be cached rather than re-calling the function
Consider caching the following:

File: NFTCollectionFactory.sol [Line 211-215](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L211-L215)
### NFTCollectionFactory.sol.adminUpdateNFTCollectionImplementation():  versionNFTCollection.toString() should be cached(Saves ~944 gas)
Using `yarn test`                                                                     
Average Gas Before: 184716                          
Average Gas After:    183772

Using `yarn test-gas-stories`
Average Gas Before: 187159                                        
Average Gas After:    186215

```solidity
    INFTCollectionInitializer(_implementation).initialize(
      payable(address(rolesContract)),
      string.concat("NFT Collection Implementation v", versionNFTCollection.toString()),
      string.concat("NFTv", versionNFTCollection.toString())
    );
```

In the above , versionNFTCollection.toString() is being evaluated twice, which just wastes gas

### Recommenation
The code should be modified as shown below
```solidity
    string memory temp = versionNFTCollection.toString();
    INFTCollectionInitializer(_implementation).initialize(
      payable(address(rolesContract)),
      string.concat("NFT Collection Implementation v", temp),
      string.concat("NFTv", temp)
    );
```

File: NFTCollectionFactory.sol [Line 237-247](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L237-L247)
### NFTCollectionFactory.sol.adminUpdateNFTDropCollectionImplementation(): versionNFTDropCollection.toString() should be cached(Saves ~953 gas)
Using `yarn test`
Average Gas Before: 343868
Average Gas After:    342915

Using `yarn test-gas-stories`
Gas Before: 346311
Gas After:   345358

```solidity
    INFTDropCollectionInitializer(_implementation).initialize(
      payable(address(this)),
      string.concat("NFT Drop Collection Implementation v", versionNFTDropCollection.toString()),
      string.concat("NFTDropV", versionNFTDropCollection.toString()),
      "ipfs://bafybeibvxnuaqtvaxu26gdgly2rm4g2piu7b2tqlx2dsz6wwhqbey2gddy/",
      0x1337000000000000000000000000000000000000000000000000000000001337,
      1,
      address(0),
      payable(0)
    );
  }
```

In the above , versionNFTDropCollection.toString() is being evaluated twice, which just wastes gas

### Recommendation
The code should be modified as shown below
```solidity
    string memory temp = versionNFTDropCollection.toString();
    INFTDropCollectionInitializer(_implementation).initialize(
      payable(address(this)),
      string.concat("NFT Drop Collection Implementation v", temp),
      string.concat("NFTDropV", temp),
      "ipfs://bafybeibvxnuaqtvaxu26gdgly2rm4g2piu7b2tqlx2dsz6wwhqbey2gddy/",
      0x1337000000000000000000000000000000000000000000000000000000001337,
      1,
      address(0),
      payable(0)
    );
  }
```

## Emitting storage values instead of the memory one.(Saves ~900gas ) 
**The amount saved is dependent on the string length(ipfs paths ) and as such, the gas cost was very inconsitent as the `yarn test-gas-stories` and `yarn test` gave different values.
After consulting one of the  Developers from the Foundation team(@HardlyDifficult) he responded with the following.**
>>"fyi i updated the gas story test to use an actual ipfs link and savings is reported as ~900. "

If we run a normal `yarn test`  the savings are around 701 gas

Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

File: NFTDropCollection.sol [Line 242](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L242)
```solidity
      function updatePreRevealContent(string calldata _baseURI, bytes32 _postRevealBaseURIHash)
    external
    validBaseURI(_baseURI)
    onlyWhileUnrevealed
    onlyAdmin
  {
    require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");


    postRevealBaseURIHash = _postRevealBaseURIHash;
    baseURI = _baseURI;
    emit URIUpdated(baseURI, postRevealBaseURIHash); //@audit: we should emit the cached values
  }
```
**baseURI** and **postRevealBaseURIHash** are storage variables

We should emit `_postRevealBaseURIHash`  instead of `postRevealBaseURIHash`  and also emit `_baseURI` instead of `baseURI`

## Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
NB: *Other than internal/private functions, the gas estimates are estimated directily by running the test cases and comparing the average gas before caching and after caching. For internal functions, the estimates are based on the number of SLOADs being saved, factoring in the mload that would replace it*

File: NFTCollectionFactory.sol [Line 202-218](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L202-L218)
### NFTCollectionFactory.sol.adminUpdateNFTCollectionImplementation(): versionNFTCollection should be cached(Saves ~261 gas)
Using `yarn test-gas-stories`
Gas before: 187159
Gas After:    186898
```solidity
  function adminUpdateNFTCollectionImplementation(address _implementation) external onlyAdmin {
    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
    implementationNFTCollection = _implementation;
    unchecked {
      // Version cannot overflow 256 bits.
      versionNFTCollection++;
    }


    // The implementation is initialized when assigned so that others may not claim it as their own.
    INFTCollectionInitializer(_implementation).initialize(
      payable(address(rolesContract)),
      string.concat("NFT Collection Implementation v", versionNFTCollection.toString()),//@audit use cached value of versionNFTCollection
      string.concat("NFTv", versionNFTCollection.toString())//@audit use cached value of versionNFTCollection
    );


    emit ImplementationNFTCollectionUpdated(_implementation, versionNFTCollection);//@audit emit a cached value of versionNFTCollection
  }
 ```
 
 SLOAD 1: [Line 213](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L213)
 SLOAD 2: [Line 214](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L214)
 SLOAD 3: [Line 217](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L217)
 

File: NFTCollectionFactory.sol [Line 226-247](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L226-L247)
### NFTCollectionFactory.sol.adminUpdateNFTDropCollectionImplementation(): versionNFTDropCollection should be cached (Saves ~252 gas)
Using `yarn test-gas-stories`
Gas before:346311
Gas After: 346059

```solidity
  function adminUpdateNFTDropCollectionImplementation(address _implementation) external onlyAdmin {
    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
    implementationNFTDropCollection = _implementation;
    unchecked {
      // Version cannot overflow 256 bits.
      versionNFTDropCollection++; //@audit: we could increment the cached value here then assign it afterwards
    }


    emit ImplementationNFTDropCollectionUpdated(_implementation, versionNFTDropCollection);//@audit use cached value of versionNFTDropCollection


    // The implementation is initialized when assigned so that others may not claim it as their own.
    INFTDropCollectionInitializer(_implementation).initialize(
      payable(address(this)),
      string.concat("NFT Drop Collection Implementation v", versionNFTDropCollection.toString()),//@audit use cached value of versionNFTDropCollection
      string.concat("NFTDropV", versionNFTDropCollection.toString()),//@audit use cached value of versionNFTDropCollection
      "ipfs://bafybeibvxnuaqtvaxu26gdgly2rm4g2piu7b2tqlx2dsz6wwhqbey2gddy/",
      0x1337000000000000000000000000000000000000000000000000000000001337,
      1,
      address(0),
      payable(0)
    );
  }
```

SLOAD 1: [Line 234](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L234)
SLOAD 2: [Line 239](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L239)
SLOAD 3: [Line 240](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L240)

File: NFTDropCollection.sol [Line 171-187](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L171-L187)
### NFTDropCollection.sol.mintCountTo(): latestTokenId should be cached (Saves ~279 gas this might be higher as the storage value is also read inside a for loop repeatedly)
using `yarn test`
Average Gas Before: 75217
Average Gas After: 74938
```solidity
  function mintCountTo(uint16 count, address to) external onlyMinterOrAdmin returns (uint256 firstTokenId) {
    require(count != 0, "NFTDropCollection: `count` must be greater than 0");


    unchecked {
      // If +1 overflows then +count would also overflow, unless count==0 in which case the loop would exceed gas limits
      firstTokenId = latestTokenId + 1; //@audit: latestTokenId should be cached
    }
    latestTokenId = latestTokenId + count;//@audit: latestTokenId should be cached
    require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");


    for (uint256 i = firstTokenId; i <= latestTokenId; ) {//@audit: latestTokenId should be cached
      _mint(to, i);
      unchecked {
        ++i;
      }
    }
  }
```
SLOAD 1: [Line 176](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L176)
SLOAD 2: [Line 178](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L178)
SLOAD(repeatedly in the for loop)[Line 181](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L181)

The Storage value `latestTokenId`  is then being read repeatedly inside a for loop which means the gas spent could be signigicantly higher than stated above

**For the Private/internal functions, an exact gas amount saved wasn't possible to determine and the estimates shown are based on the number of SLOADs that would be saved as a result of caching - remember 1 SLOAD is around 100 gas ** 

File: NFTCollection.sol [Line 332-337](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L332-L337)
### NFTCollection.sol.\_baseURI(): baseURI_ should be cached (Saves 1 SLOAD - 94 gas)

```solidity
  function _baseURI() internal view override returns (string memory) {
    if (bytes(baseURI_).length != 0) { //@audit - SLOAD 1 (should use a cached value)
      return baseURI_; //@audit - SLOAD 2 (should use a cached value)
    }
    return "ipfs://";
  }
```
SLOAD 1: [Line 333](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L333)
SLOAD 2: [Line 334](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L334)

File: SequentialMintCollection.sol [Line 86-93](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L86-L93)
### SequentialMintCollection.sol.\_updateMaxTokenId(): maxTokenId should be cached (Saves  1 SLOAD: ~94 gas)
```solidity
  function _updateMaxTokenId(uint32 _maxTokenId) internal {
    require(_maxTokenId != 0, "SequentialMintCollection: Max token ID may not be cleared");
    require(maxTokenId == 0 || _maxTokenId < maxTokenId, "SequentialMintCollection: Max token ID may not increase");//@audit SLOAD 1 and SLOAD 2 
    require(latestTokenId <= _maxTokenId, "SequentialMintCollection: Max token ID must be >= last mint");


    maxTokenId = _maxTokenId;
    emit MaxTokenIdUpdated(_maxTokenId);
  }
```

SLOAD 1,SLOAD 2: [Line 88](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L88)

File: NFTDropMarketFixedPriceSale.sol [Line 118-138](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L118-L138)
### NFTDropMarketFixedPriceSale.sol.createFixedPriceSale(): IAccessControl(nftContract) should be cached 
```solidity
  function createFixedPriceSale(
    address nftContract,
    uint80 price,
    uint16 limitPerAccount
  ) external {
    // Use the AccessControl interface to confirm the msg.sender has permissions to list.
    if (!IAccessControl(nftContract).hasRole(DEFAULT_ADMIN_ROLE, msg.sender)) {
      revert NFTDropMarketFixedPriceSale_Only_Callable_By_Collection_Owner();
    }
    // And that this contract has permission to mint.
    if (!IAccessControl(nftContract).hasRole(MINTER_ROLE, address(this))) {
      revert NFTDropMarketFixedPriceSale_Mint_Permission_Required();
    }
```
SLOAD 1:  [Line 132](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L132)
SLOAD 2: [Line 136](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L136)

File: MarketFees.sol [Line 335-355](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L335-L355)
### MarketFees.sol.internalGetMutableRoyalties(): IGetFees(nftContract) should be cached
```solidity
    if (nftContract.supportsERC165InterfaceUnchecked(type(IGetFees).interfaceId)) {
      try IGetFees(nftContract).getFeeRecipients{ gas: READ_ONLY_GAS_LIMIT }(tokenId) returns ( //@audit: Cache IGetFees(nftContract)
        address payable[] memory _recipients
      ) {
        if (_recipients.length != 0) {
          try IGetFees(nftContract).getFeeBps{ gas: READ_ONLY_GAS_LIMIT }(tokenId) returns ( //@audit: Cache IGetFees(nftContract)
            uint256[] memory recipientBasisPoints
          ) {
            if (_recipients.length == recipientBasisPoints.length) {
              return (_recipients, recipientBasisPoints);
            }
          } catch // solhint-disable-next-line no-empty-blocks
          {
            // Fall through
          }
        }
      } catch // solhint-disable-next-line no-empty-blocks
      {
        // Fall through
      }
    }
```

First Access: [Line 336](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L336)
Second Access: [Line 340](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L340)

## Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~32 - ~42 gas per access due to not having to perform the same offset calculation every time.
To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

File: NFTCollection.sol [Line 262-274](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L262-L274)
### NFTCollection.sol.\_mint(): cidToMinted\[tokenCID] should be declared as  cidToMinted storage \_cidToMinted = cidToMinted[tokenCID] (Saves ~32gas)
```solidity
  function _mint(string calldata tokenCID) private onlyCreator returns (uint256 tokenId) {
    require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");
    require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");// @audit use the cached variable
    unchecked {
      // Number of tokens cannot overflow 256 bits.
      tokenId = ++latestTokenId;
      require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
      cidToMinted[tokenCID] = true;// @audit use the cached variable
      _tokenCIDs[tokenId] = tokenCID;
      _mint(msg.sender, tokenId);
      emit Minted(msg.sender, tokenId, tokenCID, tokenCID);
    }
  }
```


File: NFTCollection.sol [Line 255-260](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L255-L260)
### NFTCollection.sol.\_burn(): \_tokenCIDs\[tokenId] should be declared as  \_tokenCIDs storage tokencid = \_tokenCIDs[tokenId] (Saves ~32 gas)

```solidity
  function _burn(uint256 tokenId) internal override(ERC721Upgradeable, SequentialMintCollection) {
    delete cidToMinted[_tokenCIDs[tokenId]];// @audit use the cached variable
    delete tokenIdToCreatorPaymentAddress[tokenId];
    delete _tokenCIDs[tokenId];// @audit use the cached variable
    super._burn(tokenId);
  }
```

First Access: [Line 256](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L256)
Second Access: [Line 258](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L258)


## Internal/Private functions only called once can be inlined to save gas(Saves ~ 20 - 40 gas)

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:
File: MarketFees.sol [Line 98-153](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L98-L153)
```solidity
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
	...
```

The above function is only used once in a child contract. See **File:NFTDropMarketFixedPriceSale.sol** [Line 210](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L210)

## Use calldata instead of memory for function parameters
When arguments are read-only on external functions, the data location should be calldata:
File: NFTCollection.sol  [Line 105-112](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L105-L112)
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

`_name`  and `symbol` should be declared using calldata  `string calldata _name and string calldata _symbol`

Note: My suggestions has already implemented on a similar function on  File: NFTDropCollection.sol  [Line 120](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L120)

## Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

File: NFTDropMarketFixedPriceSale.sol [Line 245](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L245)
```solidity
    uint256 availableToMint = limitPerAccount - currentBalance;
```

The above line cannot underflow due to the check on [Line 240](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L240) which ensures that `limitPerAccount` is greater than `currentBalance` before perfoming the mathematical operation

## Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**
File: MarketFees.sol [Line 198-200](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L198-L200)
```solidity
    for (uint256 i = 0; i < creatorShares.length; ++i) {
      creatorRev += creatorShares[i];
    }
```

The above should be modified to:
```solidity
    for (uint256 i = 0; i < creatorShares.length;) {
      creatorRev += creatorShares[i];
	  		unchecked {
					++i;
			}
    }
```

[see resource](https://github.com/ethereum/solidity/issues/10695)

## Cache the length of arrays in loops (saves ~6 gas per iteration)
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

The solidity compiler will always read the length of the array during each iteration. That is,

   1.if it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first),
   2.if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
   3.if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

This extra costs can be avoided by caching the array length (in stack):
 When reading the length of an array,  **sload** or **mload** or **calldataload** operation is only called once and subsequently replaced by a cheap **dupN** instruction. Even though mload , calldataload and dupN have the same gas cost, mload and calldataload needs an additional dupN to put the offset in the stack, i.e., an extra 3 gas. which brings this to 6 gas
 
Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:

File: MarketFees.sol [Line 126](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L126)
 ```solidity
       for (uint256 i = 0; i < creatorRecipients.length; ++i) {
```
 
**The above should be modified to**
 ```solidity
 				uint256 length = creatorRecipients.length;
       for (uint256 i = 0; i < length; ++i) {
```


**Other instances to modify**
File: MarketFees.sol [Line 198-200](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L198-L200)
```solidity
    for (uint256 i = 0; i < creatorShares.length; ++i) {
      creatorRev += creatorShares[i];
    }
```

File: MarketFees.sol [Line 482 - 484](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L482-L484)
**creatorRecipients.length** should not be looked up everytime 
```solidity
       if (creatorRecipients.length > 1) {
        unchecked {
          for (uint256 i = 0; i < creatorRecipients.length; ++i) {
```

File: MarketFees.sol [Line 503](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L503)
```solidity
      for (uint256 i = 1; i < creatorRecipients.length; ) {
```

## ++i costs less gas compared to i++ or i += 1  (~5 gas per iteration)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

```solidity
uint i = 1;  
i++; // == 1 but i == 2  
```

But ++i returns the actual incremented value:

```solidity
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:

File: NFTCollectionFactory.sol [Line 207](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L207)
```solidity
      versionNFTCollection++;
```

File: NFTCollectionFactory.sol [Line 231](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L231)
```solidity
      versionNFTDropCollection++;
```

## Comparisons: != is more efficient than > in require (6 gas less)

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check > 0 is essentially checking that the value is not equal to 0 therefore >0 can be replaced with !=0 which saves gas.

Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you're in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

I suggest changing > 0 with != 0 here:

File: NFTDropCollection.sol [Line 131](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L131)
```solidity
    require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
```

## Using bools for storage incurs overhead
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

**Instances affected include**
File: NFTCollection.sol [Line 53](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L53)
```solidity
  mapping(string => bool) private cidToMinted;
```

## A modifier used only once and not being inherited should be inlined to save gas
File: MinterRole.sol [Line 21-24](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/roles/MinterRole.sol#L21-L24)
```solidity
  modifier onlyMinterOrAdmin() {
    require(isMinter(msg.sender) || isAdmin(msg.sender), "MinterRole: Must have the minter or admin role");
    _;
  }
```

The above is only used once on the file NFTDropCollection.sol at [Line 171](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L171)

## Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

File: NFTCollectionFactory.sol [Line 291](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L286-L294)
**uint32 maxTokenId**
```solidity
  function createNFTDropCollection(
    string calldata name,
    string calldata symbol,
    string calldata baseURI,
    bytes32 postRevealBaseURIHash,
    uint32 maxTokenId,
    address approvedMinter,
    uint256 nonce
  ) external returns (address collection) {
```
File: NFTCollectionFactory.sol [Line 329](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L324-L333)
**uint32 maxTokenId**
```solidity
  function createNFTDropCollectionWithPaymentAddress(
    string calldata name,
    string calldata symbol,
    string calldata baseURI,
    bytes32 postRevealBaseURIHash,
    uint32 maxTokenId,
    address approvedMinter,
    uint256 nonce,
    address payable paymentAddress
  ) external returns (address collection) {
```

File: NFTCollectionFactory.sol [Line 368](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L368)
```solidity
  uint32 maxTokenId,
```

File: NFTCollectionFactory.sol [Line 391](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L391)
```solidity
  uint32 maxTokenId,
```

File: NFTDropCollection.sol [Line 126](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L126)
```solidity
    uint32 _maxTokenId,
```

File: NFTDropCollection.sol [Line 220-222](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L220-L222)
```solidity
  function updateMaxTokenId(uint32 _maxTokenId) external onlyAdmin {
    _updateMaxTokenId(_maxTokenId);
  }
```

File: NFTCollection.sol [Line 251-253](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L251-L253)
```solidity
  function updateMaxTokenId(uint32 _maxTokenId) external onlyCreator {
    _updateMaxTokenId(_maxTokenId);
  }
```

File: NFTCollection.sol [Line 313](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L313-L321)
**bytes4 interfaceId**
```solidity
  function supportsInterface(bytes4 interfaceId)
    public
    view
    override(ERC165Upgradeable, ERC721Upgradeable, CollectionRoyalties)
    returns (bool interfaceSupported)
  {
    // This is a no-op function required to avoid compile errors.
    interfaceSupported = super.supportsInterface(interfaceId);
  }
```

File: NFTDropMarketFixedPriceSale.sol [Line 170-174](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L170-L174)
**uint16 count**
```solidity
  function mintFromFixedPriceSale(
    address nftContract,
    uint16 count,
    address payable buyReferrer
  ) external payable returns (uint256 firstTokenId) {
```

File: SequentialMintCollection.sol [Line 86-93](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L86-L93)
**uint32 \_maxTokenId**
```solidity
  function _updateMaxTokenId(uint32 _maxTokenId) internal {
    require(_maxTokenId != 0, "SequentialMintCollection: Max token ID may not be cleared");
    require(maxTokenId == 0 || _maxTokenId < maxTokenId, "SequentialMintCollection: Max token ID may not increase");
    require(latestTokenId <= _maxTokenId, "SequentialMintCollection: Max token ID must be >= last mint");


    maxTokenId = _maxTokenId;
    emit MaxTokenIdUpdated(_maxTokenId);
  }
```

## use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of bytes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

File: NFTCollectionFactory.sol [Line 173](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L173)
```solidity
    require(rolesContract.isAdmin(msg.sender), "NFTCollectionFactory: Caller does not have the Admin role");
```

File: NFTCollectionFactory.sol [Line 182](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L182)
```solidity
    require(_rolesContract.isContract(), "NFTCollectionFactory: RolesContract is not a contract");
```

File: NFTCollectionFactory.sol [Line 203](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L203)
```solidity
    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
```

File: NFTCollectionFactory.sol [Line 227](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L227)
```solidity
    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
```

File: NFTCollectionFactory.sol  [Line 262](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L262)
```solidity
    require(bytes(symbol).length != 0, "NFTCollectionFactory: Symbol is required");
```

File: NFTDropCollection.sol [Line 88](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L88)
```solidity
    require(bytes(_baseURI).length > 0, "NFTDropCollection: `_baseURI` must be set");
```

File: NFTDropCollection.sol [Line 93](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L93)
```solidity
    require(postRevealBaseURIHash != bytes32(0), "NFTDropCollection: Already revealed");
```

File: NFTDropCollection.sol [Line 130&131](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L130-L131)
```solidity
    require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");
    require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
```

File: NFTDropCollection.sol [Line 172](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L172)
```solidity
    require(count != 0, "NFTDropCollection: `count` must be greater than 0");
```

File: NFTDropCollection.sol [Line 179](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L179)
```solidity
    require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");
```

File: NFTDropCollection.sol [Line 238](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L238)
```solidity
    require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");
```

File: NFTCollection.sol [Line 158](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L158)
```solidity
    require(tokenCreatorPaymentAddress != address(0), "NFTCollection: tokenCreatorPaymentAddress is required");
```

File: NFTCollection.sol [Line 263&264](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L263-L264)
```solidity
    require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");
    require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");
```

File: NFTCollection.sol [Line 268](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L268)
```solidity
      require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
```

File: NFTCollection.sol [Line 327](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L327)
```solidity
    require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");
```

File: NFTCollection.sol [Line 327](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L327)
```solidity
    require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");
```
File: SequentialMintCollection.sol [Line 58](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L58)
```solidity
    require(msg.sender == owner, "SequentialMintCollection: Caller is not creator");
```
File: SequentialMintCollection.sol [Line 63](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L63)
```solidity
    require(_creator != address(0), "SequentialMintCollection: Creator cannot be the zero address");
```
File: SequentialMintCollection.sol [Line 74](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L74)
```solidity
    require(totalSupply() == 0, "SequentialMintCollection: Any NFTs minted must be burned first");
```
File: SequentialMintCollection.sol [Line 87-89](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L87-L89)
```solidity
    require(_maxTokenId != 0, "SequentialMintCollection: Max token ID may not be cleared");
    require(maxTokenId == 0 || _maxTokenId < maxTokenId, "SequentialMintCollection: Max token ID may not increase");
    require(latestTokenId <= _maxTokenId, "SequentialMintCollection: Max token ID must be >= last mint");
```
File: MinterRole.sol [Line 22](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/roles/MinterRole.sol#L22)
```solidity
    require(isMinter(msg.sender) || isAdmin(msg.sender), "MinterRole: Must have the minter or admin role");
```
File: AdminRole.sol [Line 19](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/roles/AdminRole.sol#L19)
```solidity
    require(hasRole(DEFAULT_ADMIN_ROLE, msg.sender), "AdminRole: caller does not have the Admin role");
```
File: ContractFactory.sol [Line 22](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/ContractFactory.sol#L22)
```solidity
    require(msg.sender == contractFactory, "ContractFactory: Caller is not the factory");
```
File: ContractFactory.sol [Line 31](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/ContractFactory.sol#L31)
```solidity
    require(_contractFactory.isContract(), "ContractFactory: Factory is not a contract");
```
File:AddressLibrary.sol [Line 31](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/libraries/AddressLibrary.sol#L31)
```solidity
    require(contractAddress.isContract(), "InternalProxyCall: did not return a contract");
```
I suggest shortening the revert strings to fit in 32 bytes, or use custom errors.

## Use Custom Errors instead of Revert Strings to save Gas
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

File: NFTCollectionFactory.sol [Line 173](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L173)
```solidity
    require(rolesContract.isAdmin(msg.sender), "NFTCollectionFactory: Caller does not have the Admin role");
```

File: NFTCollectionFactory.sol [Line 182](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L182)
```solidity
    require(_rolesContract.isContract(), "NFTCollectionFactory: RolesContract is not a contract");
```

File: NFTCollectionFactory.sol [Line 203](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L203)
```solidity
    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
```

File: NFTCollectionFactory.sol [Line 227](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L227)
```solidity
    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
```

File: NFTCollectionFactory.sol  [Line 262](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L262)
```solidity
    require(bytes(symbol).length != 0, "NFTCollectionFactory: Symbol is required");
```

File: NFTDropCollection.sol [Line 88](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L88)
```solidity
    require(bytes(_baseURI).length > 0, "NFTDropCollection: `_baseURI` must be set");
```
File: NFTDropCollection.sol [Line 93](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L93)
```solidity
    require(postRevealBaseURIHash != bytes32(0), "NFTDropCollection: Already revealed");
```

File: NFTDropCollection.sol [Line 130&131](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L130-L131)
```solidity
    require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");
    require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
```

File: NFTDropCollection.sol [Line 172](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L172)
```solidity
    require(count != 0, "NFTDropCollection: `count` must be greater than 0");
```

File: NFTDropCollection.sol [Line 179](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L179)
```solidity
    require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");
```

File: NFTDropCollection.sol [Line 238](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L238)
```solidity
    require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");
```

File: NFTCollection.sol [Line 158](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L158)
```solidity
    require(tokenCreatorPaymentAddress != address(0), "NFTCollection: tokenCreatorPaymentAddress is required");
```

File: NFTCollection.sol [Line 263&264](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L263-L264)
```solidity
    require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");
    require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");
```

File: NFTCollection.sol [Line 268](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L268)
```solidity
      require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
```

File: NFTCollection.sol [Line 327](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L327)
```solidity
    require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");
```

File: NFTCollection.sol [Line 327](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L327)
```solidity
    require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");
```
File: SequentialMintCollection.sol [Line 58](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L58)
```solidity
    require(msg.sender == owner, "SequentialMintCollection: Caller is not creator");
```
File: SequentialMintCollection.sol [Line 63](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L63)
```solidity
    require(_creator != address(0), "SequentialMintCollection: Creator cannot be the zero address");
```
File: SequentialMintCollection.sol [Line 74](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L74)
```solidity
    require(totalSupply() == 0, "SequentialMintCollection: Any NFTs minted must be burned first");
```
File: SequentialMintCollection.sol [Line 87-89](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/collections/SequentialMintCollection.sol#L87-L89)
```solidity
    require(_maxTokenId != 0, "SequentialMintCollection: Max token ID may not be cleared");
    require(maxTokenId == 0 || _maxTokenId < maxTokenId, "SequentialMintCollection: Max token ID may not increase");
    require(latestTokenId <= _maxTokenId, "SequentialMintCollection: Max token ID must be >= last mint");
```
File: MinterRole.sol [Line 22](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/roles/MinterRole.sol#L22)
```solidity
    require(isMinter(msg.sender) || isAdmin(msg.sender), "MinterRole: Must have the minter or admin role");
```

File: ContractFactory.sol [Line 22](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/ContractFactory.sol#L22)
```solidity
    require(msg.sender == contractFactory, "ContractFactory: Caller is not the factory");
```
File:AddressLibrary.sol [Line 31](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/libraries/AddressLibrary.sol#L31)
```solidity
    require(contractAddress.isContract(), "InternalProxyCall: did not return a contract");
```

**Proof of custom errors optimizations**

```
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;

    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }

    function testFailGas() public {
        c0.stringErrorMessage();
        c1.customErrorMessage();
    }
}

contract Contract0 {
    function stringErrorMessage() public {
        bool check = false;
        require(check, "error message");
    }
}

contract Contract1 {
    error CustomError();

    function customErrorMessage() public {
        bool check = false;
        if (!check) {
            revert CustomError();
        }
    }
}
```

**Gas comparisons**
```
╭────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ Contract0 contract ┆                 ┆     ┆        ┆     ┆         │
╞════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost    ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 34087              ┆ 200             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name      ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ stringErrorMessage ┆ 218             ┆ 218 ┆ 218    ┆ 218 ┆ 1       │
╰────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
╭────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost    ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 26881              ┆ 164             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name      ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ customErrorMessage ┆ 161             ┆ 161 ┆ 161    ┆ 161 ┆ 1       │
╰────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
```
