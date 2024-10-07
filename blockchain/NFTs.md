They are a token standard ERC-721 , it means Non Fungible Token.
Internally this standard is a smart contract that has mapping from a token to his owner and from a token to its metadata. In order to avoid all the token metadata on chain, the URI is a link to the token metadata (its' an API endpoint basically) .Generally it's better to store the NFT information in a decentralized URI (IPFS is the perfect place for this, but you need to trust someone to pin your data to have it). If you need to develop an NFT game you will need some attribute of the NFT that are linked to the logic of the game, those attributes needs to be stored on-chain in order to be cryptographycally secure.

To implement your NFT contract you can import the contracts from openzeppeling and extend the ERC721 token:
```solidity
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract BasicNFT is ERC721 {}
```

openzeppelin also provide some utiliy contracts for other domains, for example the fact that a function can be called only by the owner of the contract that I implemented before can be obtained from 
```solidity
import "@openzeppelin/contracts/access/Ownable.sol";

contract MyContract is Ownable {
	...
	function myFunction onlyOwner {
		...
	}
}
```

A solution to store the actual NFT data is NFT STORAGE that uses Filecoin blockchain under the hood to store your data in a replicated and decentralized way (Filecoin is a blockchain dedicated to store data in IPFS)

A complete different alternative is to publish the data on-chain (with the cons of being really expensive, better to use SVGs to store less data on-chain).