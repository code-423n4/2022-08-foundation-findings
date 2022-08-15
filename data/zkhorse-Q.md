# QA Report

## Low

### Zero address in royalty recipients will cause payments to revert

Since `FETH` reverts on transfers/deposits/withdrawals to the [zero address](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/FETH.sol#L399), if a drop owner intentionally or accidentally adds the zero address as a royalty recipient, token mints and payment transfers will revert when `MarketFees._distributeFunds` attempts to pay creators:

[`MarketFees._distributeFunds`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/MarketFees.sol#L124-L137):

```solidity
    // Pay the creator(s)
    unchecked {
      for (uint256 i = 0; i < creatorRecipients.length; ++i) {
        _sendValueWithFallbackWithdraw(
          creatorRecipients[i],
          creatorShares[i],
          SEND_VALUE_GAS_LIMIT_MULTIPLE_RECIPIENTS
        );
        // Sum the total creator rev from shares
        // creatorShares is in ETH so creatorRev will not overflow here.
        creatorRev += creatorShares[i];
      }
    }
```

The [Manifold registry](https://github.com/manifoldxyz/royalty-registry-solidity/blob/main/contracts/overrides/RoyaltyOverrideCore.sol#L35) does disallow the zero address for recipients, but this may be possible for custom contracts.

**Impact**

Creators may accidentally brick token sales. If a malicious recipient is able to add their address to a supported royalty registry, they may be able to block token sales and payments.

**Recommendation**

Ignore any zero addresses in the `creatorRecipients` array when processing payments:

```solidity
    // Pay the creator(s)
    unchecked {
      for (uint256 i = 0; i < creatorRecipients.length; ++i) {
        if (creatorRecipients[i] == address(0)) continue;
        _sendValueWithFallbackWithdraw(
          creatorRecipients[i],
          creatorShares[i],
          SEND_VALUE_GAS_LIMIT_MULTIPLE_RECIPIENTS
        );
        // Sum the total creator rev from shares
        // creatorShares is in ETH so creatorRev will not overflow here.
        creatorRev += creatorShares[i];
      }
    }
```

### Factory template upgrades must be atomically initialized

The admin of `NFTCollectionFactory` may update the stored `implementationNFTCollection` and `implementationNFTDropCollection` by calling `[adminUpdateNFTCollectionImplementation](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L202)` and [`adminUpdateNFTDropCollectionImplementation`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L226) respectively.

[`NFTCollectionFactory#adminUpdateNFTCollectionImplementation`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L202-L218)

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
      string.concat("NFT Collection Implementation v", versionNFTCollection.toString()),
      string.concat("NFTv", versionNFTCollection.toString())
    );

    emit ImplementationNFTCollectionUpdated(_implementation, versionNFTCollection);
  }
```

[`NFTCollectionFactory#adminUpdateNFTDropCollectionImplementation`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L226-L247)

```solidity
function adminUpdateNFTDropCollectionImplementation(address _implementation) external onlyAdmin {
    require(_implementation.isContract(), "NFTCollectionFactory: Implementation is not a contract");
    implementationNFTDropCollection = _implementation;
    unchecked {
      // Version cannot overflow 256 bits.
      versionNFTDropCollection++;
    }

    emit ImplementationNFTDropCollectionUpdated(_implementation, versionNFTDropCollection);

    // The implementation is initialized when assigned so that others may not claim it as their own.
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

Note that both of these functions take the new `_implementation` address as an argument, update the value in storage, and initialize it. 

However, since the implementation contract itself is created outside of these functions, if it is not deployed and initialized atomically (e.g. using a deployment contract or Flashbots bundle), there is an opportunity for an attacker to frontrun intialization and claim ownership of the new implementation contract.

**Recommendation**

Ensure new implementation templates are deployed and initialized in a single transaction using a deployment contract or other atomic initialization mechanism.

### Collection creator can frontrun purchase and swap pre-revealed content

A collection creator can frontrun the first token purchase in their collection to recreate their collection with different content. 

**Scenario:**

- Eve creates a mintable drop collection with pre-revealed content that looks innocuous.
- Eve creates a sale with `NFTDropMarketFixedPriceSale.createFixedPriceSale`.
- Alice calls `NFTDropMarket.mintFromFixedPriceSale` to mint a token.
- Eve frontrun’s Alice’s purchase transaction, calls `NFTDropCollection.selfDestruct`, and creates a new contract at the same address with different content.

**Recommendation**

Disallow self-destructs for collections with an active sale.

## Noncritical

### Tokens may be minted to non-ERC721 recipients

`NFTCollection` uses `_mint` rather than `_safeMint` to create new tokens. This seems like an intentional design decision (so we are reporting it as a noncritical finding), but note that this will not check that the token receiver implements `onERC721Received`, and could result in tokens minted to smart contract addresses that cannot transfer them.

[`NFTCollection._mint`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollection.sol#L261-L274)

```solidity

  function _mint(string calldata tokenCID) private onlyCreator returns (uint256 tokenId) {
    require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required"); 
    require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");
    unchecked {
      // Number of tokens cannot overflow 256 bits.
      tokenId = ++latestTokenId;
      require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
      cidToMinted[tokenCID] = true;
      _tokenCIDs[tokenId] = tokenCID;
      _mint(msg.sender, tokenId);
      emit Minted(msg.sender, tokenId, tokenCID, tokenCID);
    }
  }

```

### Invalid JSON metadata in example NFTDrop

The metadata included in the [template drop collection](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L241) is invalid JSON. (Valid JSON keys must be encoded with double quotes).

```js
{
  name: "NFT Drop Collection Implementation",
  description: "Sample content for NFT drop collections",
  image: "ipfs://QmdB9mCxSsbybucRqtbBGGZf88pSoaJnuQ25FS3GJQvVAx/nft.png"
}
```

```bash
curl -s https://gateway.pinata.cloud/ipfs/bafybeibvxnuaqtvaxu26gdgly2rm4g2piu7b2tqlx2dsz6wwhqbey2gddy/1.json | tee 1.json | jq .
parse error: Invalid literal at line 2, column 7
```

## QA

Thanks for providing a preset Foundry environment for exploring your contracts. Setting up test environments to validate findings and explore is often the hardest part of these contests, and we really appreciated having a starting point with this project.

### Remove unused function

[`BytesLibrary.replaceAtIf`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/libraries/BytesLibrary.sol#L15) appears to be unused. Consider removing this function.

### Shadowed variable name

`_baseURI` is used as a parameter in a few functions in `NFTDropCollection`, but shadows the `_baseURI` state variable inherited from `ERC721Upgradeable`. Consider renaming these parameters `baseURI_` to avoid ambiguity.

[`NFTDropCollection#L87-90`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L87-L90)

```solidity
  modifier validBaseURI(string calldata _baseURI) {
    require(bytes(_baseURI).length > 0, "NFTDropCollection: `_baseURI` must be set");
    _;
  }

```

[`NFTDropCollection#L195-202`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L195-L202)

```solidity
  function reveal(string calldata _baseURI) external onlyAdmin validBaseURI(_baseURI) onlyWhileUnrevealed {
    // `postRevealBaseURIHash` == 0 indicates that the collection has been revealed.
    delete postRevealBaseURIHash;

    // Set the new base URI.
    baseURI = _baseURI;
    emit URIUpdated(_baseURI, "");
  }

```

[`NFTDropCollection#L232-243`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTDropCollection.sol#L232-L243)

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
    emit URIUpdated(baseURI, postRevealBaseURIHash);
  }

```

### Incorrect comment

This natspec comment on [`NFTCollectionFactory`](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/NFTCollectionFactory.sol#L60) should refer to ERC-1167 rather than ERC-1165:

```
* @dev This creates and initializes an ERC-1165 minimal proxy pointing to a NFT collection contract implementation.
```