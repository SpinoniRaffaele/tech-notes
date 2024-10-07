VANILLA JS WITH ETHERS
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

NEXT.JS WITH MORALIS

to initialize a next.je project run `yarn create next-app .`
Next.js is build on top of react, it uses react components underneath.
Create the components folder and add a js file for each component there.
In the component there are both HTML in js codes:
```javascript
export default function Header() {
    return (
        <nav className="p-5 border-b-2 flex flex-row">
            <h1 className="py-4 px-4 font-bold text-3xl"> 
            Decentralized Lottery
            </h1>
            <div className="ml-auto py-2 px-4">
                <ConnectButton moralisAuth={false}/>
            </div>
        </nav>
    )
}
```



Frontend package used in react: moralis, react-moralis (they include also ethers under the hood)
It provides a useMoralis() hook.
Moralis also provides a handy function: enableWeb3() that search and connects to a wallet and its opposite to disconnect: deactivateWeb3().
They give also a hook to interact with the contract:
```
useWeb3Contract({
	abi, contractAddress, functionName, params, msgValue
})
```
To import the chainId you can use the 
```javascript
const chainId = parseInt(useMoralis().chainId);
```

A hook is used to allow react to refresh the UI when the hook changes value.
In react you can add JS in the HTML code by using {//js code here}.

**useEffect** is a core hook from "react", it keeps checking values in a dependency array, if any of the value sin the array changes useEffect wil call the function provided and rerender the UI:
```javascript
useEffect(() => {console.log("Hi")}, [someHook]);
```
it actually triggers also at loading time. If it is used with an empty dependency array it will trigger at any change for any hook (dangerous!)

userState hook
this is used when you want to create a hook out of a variable (a variable that when is updated will trigger a UI refresh).
```javascript
//create a myHook hook with initial value of 0 updated with myFunction
const [myHook, myFunction] = useState("0");

//whenever i will call myFunction i will trigger the hook
```


A trick to be faster is to install web3uikit, which contains a set of ready to use components for react. For example the 'ConnectButton' will automatically handle wallet connections.

A good practice in order to have all the ABI ADDRESSES and other info that are specific to where the contract is deployed, is to have a special deploy script in the backend that generates a file (or folder with all these needed constants).
example:
```javascript
//99-update-frontend.js
const {ethers, network} = require("hardhat");
const fx= require("fs");

modules.exports = async function () {
	const raffle = await ethers.getContract("Raffle");
	const addresses = {};
	const abi = raffle.interface.format(ethes.utils.FormatTypes.json);//get the ABI
	addresses[network.config.chainId] = raffle.address;
	// then you can write the addresses and the abi to any file
}
```

HOW TO KEEP A LIST OF NFTs SAVED FOR A FRONTEND TO SHOW?

In the contract we don't want to store the array because it is gas inefficient, we will have mappings and we cannot import a whole mapping in the frontend (because it has a key for every address on the planet).

The solution is to emit the data (from the smart contract) every time a new NFT is listed.
Off chain we setup a server that listen to these events and stores them in a DB.
Isn't this centralized? -> YES
But you can do the same in a decentralized way using tools like: **The graph**.

MORALIS (careful: this is a way of centralizing the application)
**Think of it like Firebase for crypto.**
It is a web3 stack providing integration with smart contracts, server components that are used in backend to react to events that happens on-chain, and frontend components that allow frontends to interact with smart contracts. It uses MongoDB as DB.
It provides:
- Identity: login users (one user stored in DB linked with different addresses in different chains)
- real-time: setup web-hooks for events happening in the chain
- SDK: frontend library (games engines, webpage, mobile app)
- API: raw request to Moralis if you cannot use the SDK.

