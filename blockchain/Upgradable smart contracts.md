Once deployed the smart contract are immutable on the chain, 
but what if one wants to upgrade the smart contract?

There are three approaches to upgradable smart contracts:
- Parametrize: it is not really an update but we can leave some parameters as input in our smart contract function and change the logic based on that.
- social migration: just deploy a new contract and tell the community to use the new one (it happened to AAVE from v1 to v2), it's hard to move and to convince people of doing targeting the new proxy.
- PROXY: the best approach allowing to switch implementation under a fixed proxy address. These proxy use the delegatecall function (it is a low level function that executes the code implemented in the called contract but using the storage and the context of the caller contract). The proxy has a storage function containing the address of the underlying contract and it has a setter function so that we can change the underlying implementation.

## Delegatecall

```solidity
contract B {
	uint public num;

	function setVars(uint _num) public payable {
		num = num;
	}
}

contract A {
	uint public value;
	
// imagine the _contract address containing the address of B
	function setVars(address _contract, uint _num) public payable {
		(bool success, bytes memory data) = _contract.delegateCall(
			abi.encodeWithSignature("setVars(uint256)", _num)
		);
	}
}
```
Contract A will update the value of ITS storage variable `value` using the logic present in B.
Because value has the same storage slot as the variable `num` in B. The transaction will go through even if the variables have different types (something similar to javascript automatic casting is happening).

## PROBLEMS IN PROXY METHODOLOGY
- we are loosing the immutability that was a pillar of the blockchain. The admins of the proxy contract can be centralized loosing totally the decentralization of the blockchain.
- storage clashing: the reference of a storage variable is not given by the name (under the hood it is given by a position in the storage starting from 0). If I target the same name in another contract I will target a different storage location
- function selector clashes: the function selector is a number identifying the function based on it's signature, but different signatures can lead to the same function selector, leading to issues where calling something different of what expected -> this problem is solved by Transparent Proxies (admin user can only call proxy function and normal users can only call normal functions in the underlying implementation contract)

Example of proxy implementation:
