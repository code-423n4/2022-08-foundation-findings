# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead   |   5 |
| 2      | Use custom errors rather than  **require()**/**revert()** strings to save deployments gas     |  27  |
| 3      | require()/revert() strings longer than 32 bytes cost extra gas     | 27  |
| 4      | It costs more gas to initialize variables to zero than to let the default of zero be applied    |  3  |
| 5      | ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as in the case when used in for & while loops |  2  |
| 6      | <ARRAY>.length should not be looked up in every loop in a for loop |  17 |
| 7      | Functions guaranteed to revert when called by normal users can be marked payable |  17 |

## Findings

### 1- Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead :

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size as you can check [here](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html).

So use **uint256**/**int256** for state variables and then downcast to lower sizes where needed.

There are 4 instances of this issue:

```
File: contracts/mixins/collections/SequentialMintCollection.sol

27      uint32 public latestTokenId;
34      uint32 public maxTokenId;
40      uint32 private burnCounter;

File: contracts/NFTCollectionFactory.sol

80      uint32 public versionNFTCollection;
95      uint32 public versionNFTDropCollection;
```

### 2- Use custom errors rather than  **require()**/**revert()** strings to save deployments gas :

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information.

There are many lines with this issue throughout the contracts : 

File: contracts/mixins/collections/SequentialMintCollection.sol => 6 instances
File: contracts/roles/AdminRole.sol => 1 instances
File: contracts/roles/MinterRole.sol => 1 instances
File: contracts/shared/ContractFactory.sol => 2 instances
File: contracts/NFTCollection.sol => 5 instances
File: contracts/NFTCollectionFactory.sol => 5 instances
File: contracts/NFTDropCollection.sol => 7 instances

### 3- **require()**/**revert()** strings longer than 32 bytes cost extra gas :

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information.

There are many lines with this issue throughout the contracts : 

File: contracts/mixins/collections/SequentialMintCollection.sol => 6 instances
File: contracts/mixins/roles/AdminRole.sol => 1 instances
File: contracts/mixins/roles/MinterRole.sol => 1 instances
File: contracts/mixins/shared/ContractFactory.sol => 2 instances
File: contracts/NFTCollection.sol => 5 instances
File: contracts/NFTCollectionFactory.sol => 5 instances
File: contracts/NFTDropCollection.sol => 7 instances

### 4-  It costs more gas to initialize variables to zero than to let the default of zero be applied  :

If a variable is not set/initialized, it is assumed to have the default value (0 for uint or int, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 3 instances of this issue:

```
File: contracts/mixins/shared/MarketFees.sol 

126     for (uint256 i = 0; i < creatorRecipients.length; ++i)
198     for (uint256 i = 0; i < creatorShares.length; ++i)
484     for (uint256 i = 0; i < creatorRecipients.length; ++i)
```

### 5- ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 4 instances of this issue:

```
File: contracts/mixins/shared/MarketFees.sol 

126     for (uint256 i = 0; i < creatorRecipients.length; ++i)
198     for (uint256 i = 0; i < creatorShares.length; ++i)
```

### 6- <ARRAY>.length should not be looked up in every loop in a for loop 

There are 4 instances of this issue:

```
File: contracts/mixins/shared/MarketFees.sol 

126     for (uint256 i = 0; i < creatorRecipients.length; ++i)
198     for (uint256 i = 0; i < creatorShares.length; ++i)
484     for (uint256 i = 0; i < creatorRecipients.length; ++i)
503     for (uint256 i = 1; i < creatorRecipients.length; )
```

### 7- Functions guaranteed to revert when called by normal users can be marked payable :

If a function modifier such as **onlyAdmin** is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 17 instances of this issue:

```
File: contracts/NFTCollection.sol 

119     function burn(uint256 tokenId) public override onlyCreator
129     function mint(string calldata tokenCID) external returns (uint256 tokenId)
142     function mintAndApprove(string calldata tokenCID, address operator) external returns (uint256 tokenId)
154     function mintWithCreatorPaymentAddress(string calldata tokenCID, address payable tokenCreatorPaymentAddress) public
174     function mintWithCreatorPaymentAddressAndApprove(string calldata tokenCID, address payable tokenCreatorPaymentAddress, address operator) external
230    function selfDestruct() external onlyCreator 
238    function updateBaseURI(string calldata baseURIOverride) external onlyCreator
251     function updateMaxTokenId(uint32 _maxTokenId) external onlyCreator

File: contracts/NFTCollectionFactory.sol 

202    function adminUpdateNFTCollectionImplementation(address _implementation) external onlyAdmin 
226    function adminUpdateNFTDropCollectionImplementation(address _implementation) external onlyAdmin
142     function mintAndApprove(string calldata tokenCID, address operator) external returns (uint256 tokenId)

File: contracts/NFTDropCollection.sol 

159    function burn(uint256 tokenId) public override onlyAdmin
171     function mintCountTo(uint16 count, address to) external onlyMinterOrAdmin returns (uint256 firstTokenId)
195    function reveal(string calldata _baseURI) external onlyAdmin validBaseURI(_baseURI) onlyWhileUnrevealed
209    function selfDestruct() external onlyCreator 
220    function updateBaseURI(string calldata baseURIOverride) external onlyCreator
232    function updatePreRevealContent(string calldata _baseURI, bytes32 _postRevealBaseURIHash) external validBaseURI(_baseURI) onlyWhileUnrevealed onlyAdmin
```