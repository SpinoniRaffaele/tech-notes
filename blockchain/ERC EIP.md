EIP: ethereum improvement proposal (any idea of improvement to build on top of ethereum)
ERC ethereum request for comments: it's the result of the improvement proposal (the ERC itself defines the new standard to be implemented)

These ERC-20 are not native in the blockchain
ERC-20 Token is a smart contract representing a token, it is deployed on the ethereum blockchain.
In order to build such a token is enough to build and deploy a smart contract that follow the ERC-20 standard (it implements a certain interface)


You can get the boilerplate of the ERC-20 Contract on openzeppelin (the standard lib of solidity)
`yarn add @openzeppelin/contracts`

and then writing the contract is super simple:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract OurToken is ERC20 {
    constructor(uint256 initialSupply) ERC20("OurToken", "OT") {
        _mint(msg.sender, initialSupply);
    }
}
```

mint is the function that generates more tokens; here we assign the new ones to the contract deployer.