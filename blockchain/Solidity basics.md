https://remix.ethereum.org
Remix is a web IDE to learn solidity. Solidity files have the .sol extension.
Every solidity files start with the version of solidity used
`pragma solidity ^0.8.7;  //any minor version starting from 0.8.0` 

Define a contract (like a class)
```solidity
contract SimpleStorage {
    bool hasFavoriteNumber = true;
    uint public favoriteNumber = 0;
    
    function store(uint256 _favoriteNumber) public {
        favoriteNumber = _favoriteNumber;
    }
    // a view function doesn't allow modification of state
    // a pure function doesn't allow neither modification neither read
	function store() public view returns(uint) {
        return favoriteNumber;
    }
}
```

pure and view functions are not modifying state and they are not creating a transaction in the chain (no fees).
they do however cost gas if they are called from within a transaction

primitive types: boolean, uint8, ..., uint256 (the default), int, address, bytes, string

in solidity there is a default value assigned to every variable if not initialized (0 for numbers).
The daful visibility is internal (scoped to the contract or children contracts), if you set a variable as public you automatically create a getter for it

Array and structs

```solidity
struct People {
	uint favoriteNumber;
	string name;
}
// all the elements in the struct are indexed starting from 0
People public person = People({
        favoriteNumber: 2, name: "raf"});

People[] public people; //dynamic array

// resetting an array to an empty one: people = new People[](0);

function addPerson(string memory _name, uint _favoriteNum) public {
	People person = People(_favoriteNum, _name)
	people.push(person);
}


//map type
mapping(string => uint) public nameToNumber;

function addPerson(string memory _name, uint _favNum) public {
	nameToNumber[_name] = _favNum;
}
```


In EVM you can access and store info in:
- stack
- memory (temporarily inside the function)
- storage (they persist outside a function, it is automatically set for global function in the contract)
- calldata (temporarily inside the function, it is READONLY)
- code
- logs
Those memory location are valid only for struct, array or mapping.  (string are secretly arrays).
For the primitive types solidity know already where to put them.

Import a contract from within a contract:
`import "./name.sol"`
the two contracts can have different solidity versions, you need to make sure that the versions are overlapping in order to be able to compile the contract.
You can also import from external sources, NPM:
```
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol"
```

To interact with the imported contract you need its address and its ABI (application binary interface).
Inheritance in contracts:
```solidity
contract Child is Parent {
	// override function: in the parent the function needs to have the 'virtual' 
	// keyword, here it needs the 'override'
	function store(uint256 _favNumber) public override {
        favoriteNumber = _favNumber;
    }
}
```

You can tag a function with the keyword 'payable' to mark it as a function that will transmit value (native tokens like ETH).
You can add constraints to a payable function using 'require':
 requires that the value is et least 1 * 10^18 Wei (1 ETH)
`require(msg.value > 1e18, "Didn't send enough!");`

NB: msg is the global variable that contains info about the call of the transaction:
msg.value = how much native currency has been sent
msg.sender = the address of the sender
msg.to = the address of the receiver
msg.body = the data on the body of the transaction
another global variable available is the `block` containing info about the block (more info in the solidity docs)
for example the block.timestamp is the timestamp of the block globally available

if the require condition is not met, the function will REVERT: undo any action done before and return the remaining gas fee back. (it's like rolling back a transaction)

In order to interact from an external contract (like an oracle) you need its ABI and the address.

INTERFACE:
```solidity
interface MyInterface {
	function myFunction(uint256 b) external returns(uint256);
}
```
How to create a reference from a contract having its address:
you need to import its ABI (import "@external...";)
and to create the variable:
`ExternalType myVar = ExternalInterface(0x...)`

when doing math in solidity use integer values that are actual values multiplied by 10^18 to avoid rounding errors of floating numbers.

From version 0.8 of solidity the uint and int are checked, so if you exceed the maximum number representable by the variable, the function will fail (a simple add or substract could fail).
You can override this and have strange results with `unchecked` keyword.

LIBRARY:
no contract, just logic, no transactions and no ether.

```solidity
library PriceConverter {

        function someFunction(uint param) public view returns(uint256) {
        return 1;
    }
}
```

You can then call it in different ways:
```solidity
PriceConverter.someFunction(1);

//OR allowing to call PriceConverter directly over uint variables
using PriceConverter for uint

msg.value.getConversionRate()
```


HOW TO TRANSFER ETH (from the contract to an address)
- transfer function: `payable(destinationAddress).transfer(address(this).balance);` (it is capped at 2300 gas, if it overflow it will throw an error)
- send: `bool success = payable(address).send(address(this).balance);require(success);` it's essential to require the success  (send doesn't throw an error but returning false in case of gas overflow, over 2300 gas)
- call: `(bool success, bytes dataReturned) = payable(address).call{value: address(this).balance}(""); require(success, "Failed")` this works as send, it is the RECOMMENDED way


MODIFIERS
you can create your own keyword in solidity
```solidity
    modifier onlyOwner {
        require(msg.sender == owner, "Sender is not owner");
        _;  // this means go ahed and execute the function
    }

	//this function gets executed only if the require in the modifier is satisfied
	function myFunc() public onlyOwner {
	}
```

CONSTANT VARIABLES:
in order to save gas you should use constants when possible (huge diffence)
`uint public constant MY_VAR = 1;`

Variable that are set only once (like only in the constructor) can be set to IMMUTABLE to save gas:
`address public immutable mMY_ADDRESS;`

Special Functions:
the constructor itself is a special function
receive is called anytime we send a transaction to the contract with CALLDATA empty
```solidity
receive() external payable {
	// code here
}
```

fallback is called anytime we call a transaction on the contract and it doesn't map to a valid function.
```solidity
fallback() external payable {}
```

STYLE GUIDE
order: 
- pragma, 
- imports, 
- error codes (which should be named ContractName__ErrorName), 
- interfaces, 
- contracts
	- type declaration (`using PriceConverter for uint256`)
	- state variables
	- modifier and events
	- constructor
	- receive and fallback
	- public
	- private
	- views

the doc is specified in NatSpec specification.
ex:
```natspec
/**
* @title
* @author
* @notice
* @dev
*/
```


STORAGE OF VARIABLES
the variables global to the contract, they are saved sequentially in an array called *Storage*,
each slot in the Storage is 32byte long, for dynamic elements, the elements itself stores his own length, the elements contained are stored in the position given by the hash of the single value inside the collection:
At [0] you have the length of the array, 
At[hash(1)] you have the value 1
for mapping the first element in the Storage is blank.
THIS MEANS THAT PRIVATE VARIABLES ARE ALLOCATED IN KNOWN POSITION SO EVERYBODY CAN LOOK AT THEM
Reading and Writing in Storage cost a lot of Gas!

A convention for these storage variables is to prepend them with *s_* to explicit the fact that they cost a lot of gas, *i_* to identify immutables
When working a lot with storage variables (not mappings, they cannot be in memory) is a good practice to create an in memory copy:
```solidity
address[] memory inMemAddresses = s_adresses;
```


Constant and Immutable are saved in the contract definition itself, no need to use the storage


Variables defined in a function scope are in another memory specific for them, this area is identified with 'memory'

EVENTS
The EVM emits data in a section called Logs, a piece of the Log data structure is the Event.
The logs are not accessible from the smart contracts (they are not into the storage space)

The events are used by off-chain products to update once the transaction event is triggered by the chain, The event logs contains:
- the address of the account or the contract from which is it emitted
- the topics (indexes data emitted, max 3) costly to put in the log in terms of gas, but easier to query for
- data: non indexed data, you will need to parse them (they are ABI encoded, Hex)

Example of event:
```solidity
event MyEvent(address indexed param);

//then in a function
emit MyEvent(msg.sender);
```

In js you can access those events data starting from the *transactionReceipt*:
```javascript
txReceipt.logs[1].args //both indexed and non-indexed
```

ENUMS
it's a new type, defined as first thing INSIDE the contract:
```
enum MyEnum {
VALUE1, VALUE2
}

//MyEnum.VALUE1;
```