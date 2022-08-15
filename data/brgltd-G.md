# Use custom error instead of require() strings

Replacing `require()` strings with custom error would yield the following improvements on gas deployments (diff formatted for readability).

```
$ git diff --no-index gas-stories-original.txt gas-stories-with-custom-errors.txt
diff --git a/gas-stories-original.txt b/gas-stories-with-custom-errors.txt
index e2b107d..73442b8 100644
--- a/gas-stories-original.txt
+++ b/gas-stories-with-custom-errors.txt
@@ -1,6 +1,6 @@
Deployments
-                       Avg
-NFTCollection         2899976
-NFTDropCollection     3409650
+                       Avg
+NFTCollection         2704389
+NFTDropCollection     3056772
```

There are 12 instances of this issue

```
File: contracts/NFTCollection.sol
158: require(tokenCreatorPaymentAddress != address(0), "NFTCollection: tokenCreatorPaymentAddress is required");
263: require(bytes(tokenCID).length != 0, "NFTCollection: tokenCID is required");
264: require(!cidToMinted[tokenCID], "NFTCollection: NFT was already minted");
268: require(maxTokenId == 0 || tokenId <= maxTokenId, "NFTCollection: Max token count has already been minted");
327: require(_exists(tokenId), "NFTCollection: URI query for nonexistent token");
```

```
File: contracts/NFTDropCollection.sol
88: require(bytes(_baseURI).length > 0, "NFTDropCollection: `_baseURI` must be set");
93: require(postRevealBaseURIHash != bytes32(0), "NFTDropCollection: Already revealed");
130: require(bytes(_symbol).length > 0, "NFTDropCollection: `_symbol` must be set");
131: require(_maxTokenId > 0, "NFTDropCollection: `_maxTokenId` must be set");
172: require(count != 0, "NFTDropCollection: `count` must be greater than 0");
179: require(latestTokenId <= maxTokenId, "NFTDropCollection: Exceeds max tokenId");
238: require(_postRevealBaseURIHash != bytes32(0), "NFTDropCollection: use `reveal` instead");
```
