
# QA
# Low

## Missing checks for address(0x0) when assigning values to address state variables
### Summary
Zero address should be checked for state variables and some parameters in functions like mints, withdrawals... A zero address can lead into problems.
### Github Permalinks
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L87

### Mitigation
Check zero address for state variables before assigning to them a value
Check in token/eth/permission assigned related the 0 address 


## Implementation of the comment spec is confusing
### Summary:
It says "for internal use only" However, external visibility is enabled. If "internal use only" means the organization, use a source of access control such as a onlyOwner modifier.
If internal is for visibility, change the try catch scenario into a scenario with regular if checks so internal visibility can be used.

###  Github Permalinks:
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L212-L223
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L273-L283
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L225-L271

###  Mitigation:
Adjust the code to the specifications.


# Informational

## Use of magic numbers is confusing 
###  Summary:
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
###  Details:
Values are hardcoded and would be more readable and maintainable if declared as a constant

There are 2 cases that are commented in the functions, but they would be more readable with proper constant naming (for example: SIGNATURE_LENGTH or ADDRESS_LENGTH):
- Signature length is used as a hardcoded number
- Address length is used as a hardcoded number


###  Github Permalinks:
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/BytesLibrary.sol#L39-L44
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/BytesLibrary.sol#L25

###  Mitigation:
Replace magic hardcoded numbers with declared constants.


## Missing indexed event parameters
###  Summary:
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
###  Details:
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
###  Github Permalinks:
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L70
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L85

###  Mitigation:
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.


## Missing Natspec 
 ###  Summary: 
 Missing Natspec and regular comments affect readability and maintainability of a codebase. 
 ###  Details: 
 Contracts has partial or full lack of comments
 ###  Github Permalinks: 
 #### Some Natspec @params
- _assumePrimarySale
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L83

#### Natspec missing (@param, @return...) 
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L212-L223
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/CollectionRoyalties.sol#L18-L49
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/CollectionRoyalties.sol#L62-L91
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L95-L153
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L225-L530
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L281-L304
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L244-L259
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L57-L67
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L95-L111
https://github.com/code-423n4/2022-08-foundation/blob/200b16bc17b55f1558e456963a833ff00c22610e/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L295-L296
https://github.com/code-423n4/2022-08-foundation/blob/200b16bc17b55f1558e456963a833ff00c22610e/contracts/NFTDropMarket.sol#L104-L150
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L255-L274
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L295-L337
https://github.com/code-423n4/2022-08-foundation/blob/200b16bc17b55f1558e456963a833ff00c22610e/contracts/NFTCollectionFactory.sol#L448-L450
https://github.com/code-423n4/2022-08-foundation/blob/200b16bc17b55f1558e456963a833ff00c22610e/contracts/NFTCollectionFactory.sol#L386-L423

#### More documentation needed
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/MinterRole.sol#L21-L29
 https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L87-L95
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/ContractFactory.sol#L21-L24
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/AddressLibrary.sol#L34-L39
https://github.com/code-423n4/2022-08-foundation/blob/200b16bc17b55f1558e456963a833ff00c22610e/contracts/NFTCollectionFactory.sol#L172-L175

#### Same comments but different function name
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketSharedCore.sol#L24-L36

 ###  mitigation
 Add @param descriptors
 Add @return descriptors
 Add Natspec comments. 
 Add inline comments. 
 Add comments for what the contract does

## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions
Also is good to mention that:
- state variables order affects to gas in the same way as ordering structs for saving storage slots.
- the more used functions in the contract can save gas being ordered the first
### Details
Modifier `onlyAdmin` is inserted after functions

### github permalink
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/AdminRole.sol#L13-L21

### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.15/style-guide.html


## Maximum line length exceeded
### Summary: 
Long lines should be wrapped to conform with Solidity Style guidelines.
### Details: 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks:
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropMarket.sol#L98
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropMarket.sol#L106
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/mixins/shared/Constants.sol#L51
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/mixins/shared/FETHNode.sol#L40
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/BytesLibrary.sol#L12
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketSharedCore.sol#L11
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketSharedCore.sol#L20
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketSharedCore.sol#L27
https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/mixins/nftDropMarket/NFTDropMarketCore.sol#L9

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L291
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L279
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L268
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L249
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L246
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L235
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L228
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L221
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L204
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L205
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L210
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L197
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L188
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L184
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L185
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L158
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L154
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L148
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L142
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L78
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L75
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTCollection.sol#L26

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L522
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L486
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L460
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L454
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L434
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L415
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L315
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L299
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L283-L285
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L238
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L228
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L167
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L162
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L163
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L149
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L146
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L142
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L91
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L69
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L60
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/shared/MarketFees.sol#L28

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L229-L230
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L218
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L207
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L195
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L175
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L171
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L169
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L113-L114
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/NFTDropCollection.sol#L82-L83

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L88-L89
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L84
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L62
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L46
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/SequentialMintCollection.sol#L13

https://github.com/code-423n4/2022-08-foundation/blob/b4e1dc6a9a434d576cb8f74676e7dbb65dacc98d/contracts/libraries/AddressLibrary.sol#L20

https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L444-L445
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L441
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L432-L433
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L429
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L426
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L396
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L359-L360
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L355-L356
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L348-L350
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L320
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L316-L317
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L311
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L283
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L279-L280
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L274
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L264
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L254
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L251
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L221
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L210
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L197
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L189
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L173
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L151-L152
https://github.com/code-423n4/2022-08-foundation/blob/254d384116d22266b12916f1aeb46b9ef0143f1f/contracts/NFTCollectionFactory.sol#L60

https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L232
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L218
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L198
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L168
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L165-L166
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L156
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L92
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/nftDropMarket/NFTDropMarketFixedPriceSale.sol#L67
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/AdminRole.sol#L19
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/roles/MinterRole.sol#L22
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/CollectionRoyalties.sol#L80
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/CollectionRoyalties.sol#L21
https://github.com/code-423n4/2022-08-foundation/blob/7d6392498e8f3b8cdc22beb582188ffb3ed25790/contracts/mixins/collections/CollectionRoyalties.sol#L17

### Mitigation: 
Comments and lines of code should be wrapped to a maximum of 79 (or 99) characters to help readers easily parse the comments.

