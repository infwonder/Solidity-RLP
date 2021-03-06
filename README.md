# RLP decoder/reader
[![Build Status](https://travis-ci.com/hamdiallam/Solidity-RLP.svg?branch=master)](https://travis-ci.com/hamdiallam/Solidity-RLP)  
Currently support for solidity **v0.4.24**  
> Please raise issues for bugs, and solidity updates. I will be monitoring the solidity changelogs and updating this package accordingly

## Installation
1. `npm install solidity-rlp` in the project directory. Make sure to install through npm for prompt updates!
2. `import "solidity-rlp/contracts/RLPReader.sol"` in the desired smart contract.

_See the example smart contract below_

## Usage
The reader contract provides an interface to first take RLP encoded bytes and convert them into
an internal data structure, `RLPItem` through the function, `toRlpItem(bytes)`. This data structure can then be
destructured into the desired data types.

Transformations(All take an RLPItem as an arg):  
1. `toList(RLPItem)` : returns a list of RLPItems, `RLPReader.RLPItem[]`
2. `toBytes(RLPItem)` : returns the payload in bytes
3. `toAddress(RLPItem)` : returns the encoded address. Must be 20 bytes long.
4. `toUint(RLPItem)` : returns the encoded uint
5. `toBoolean(RLPItem)`: returns the encoded boolean
6. `toRlpBytes(RLPItem)`: returns the raw rlp encoded byte form

**Note**: The reader contract only provides only these conversion functions. All other solidity data types can be derived from
this base. For example, a `bytes32` encoded data type is equivalent to `bytes32(toUint(RLPItem))`. Start with a uint and convert from there

## Example
```solidity
import "solidity-rlp/contracts/RLPReader.sol"

contract SomeContract {
    
    // optional way to attach library functions to these data types.
    using RLPReader for RLPReader.RLPItem;
    using RLPReader for bytes;

    // lets assume that rlpBytes is an encoding of [[1, "nested"], 2, 0x<Address>]
    function someFunctionThatTakesAnEncodedItem(bytes memory rlpBytes) public {
        RLPReader.RLPItem[] memory ls = rlpBytes.toRlpItem().toList(); // must convert to an rlpItem first!

        RLPReader.RLPItem memory item = ls[0] // the encoding of [1, "nested"].
        item.toList()[0].toUint() // 1
        string(item.toList()[1].toBytes) // "nested"

        ls[1].toUint() // 2
        ls[2].toAddress() // 0x<Address>
    }
}
```


## Tests
1. `git clone https://github.com/hamdiallam/solidity-rlp && cd solidity-rlp`
2. `npm install`
3. `npm install -g truffle ganache-cli` installed globally for the dev envirnoment
4. `ganache-cli` run in a background process or seperate terminal window.
4. `truffle compile && truffle test`

