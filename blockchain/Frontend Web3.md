You generally have one repo for the backend (with smart contracts) and one for the frontend.
If a browser have a wallet extension, it will have an object appended in the window.
in case of metamask it is:
`window.ethereum` or `window.solana`
Every wallet exposes a node that we can use to connect to the chain.

Connect to the wallet:
```javascript
window.ethereum.request({method: "eth_requestAccounts"});
```

You also need to import ethers in the frontend to manipulate the provider, the wallets and interact with the contracts.

You need to run the hardhat node and then connect metamask to your own node
Then you need to import into metamask an account created by hardhat (with some eth in it) by using it's private key

in order to interact with a contract you need his address and his ABI:
```javascript
const provider = new ethers.providers.Web3Provider(window.ethereum);
    const signer = provider.getSigner(); //the wallet connected
    const contract = new ethers.Contract(contractAddress, ABI, signer);
    await contract.fund({ value: "100000000000000000" });
```