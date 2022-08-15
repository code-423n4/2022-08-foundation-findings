## Table of Contents:
L-01 Open TODOs
L-02 abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

N-01 NatSpec is incomplete
N-02 Event is missing indexed fields
N-03 Remove commented out code




## L-01 Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

There is 1 instances in 1 file:
File: contracts/mixins/shared/MarketFees.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L193


## L-02: abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions 
(e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). 
“Unless there is a compelling reason, abi.encode should be preferred”. 
If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32()
https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739

There is 1 instance:
File: contracts/NFTCollectionFactory.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/NFTCollectionFactory.sol#L449



## N-01 NatSpec is incomplete

There 7 instances in 3 files:
File: contracts/libraries/BytesLibrary.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L11-L15
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/BytesLibrary.sol#L35-L38

File: contracts/libraries/ArrayLibrary.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/ArrayLibrary.sol#L9-L13
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/libraries/ArrayLibrary.sol#L21-L25

File: contracts/mixins/collections/CollectionRoyalties.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/CollectionRoyalties.sol#L26-L29
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/CollectionRoyalties.sol#L37-L39
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/collections/CollectionRoyalties.sol#L62-L64


## N-02 Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

There is 1 instance in 1 file:
Fille: contracts/mixins/shared/MarketFees.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L71-L77


## N-03 Remove commented out code

There is 1 instance in 1 file:
File: contracts/mixins/shared/MarketFees.sol
https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/MarketFees.sol#L35-L41


