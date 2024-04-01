VSCode extension: Solidity (from Nomic fundation)
then installed yarn with `corepack enable`
then add solc (SOL compiler): `yarn add solc@version`
compile with solc: `yarn solcjs --bin --abi --include-path node_modules/ --base-path . -o . SimpleStorage.sol` (we want the bin and abi compiled files starting from SimpleStorage.sol file).  The compilation creates a bin and an ABI file.

We now need a Virtual Blockchain running our contract to test -> install GANACHE desktop app

Ether.js is a js library wrapper for API calls over any EVM compatible chain. An alternative to this is web3.js.

Example of deployment of a contract over local chain using ether.js
```javascript
const ethers = require("ethers");
const fs = require("fs-extra");

async function main() {
  const provider = new ethers.JsonRpcProvider("http://127.0.0.1:7545");
  const wallet = new ethers.Wallet(
    "0x1096b703d479b26956ea94b0104e3a21649382c19f7328ba03479a0222c730f8",
    provider
  );
  const abi = fs.readFileSync("./SimpleStorage_sol_SimpleStorage.abi", "utf8");
  const binary = fs.readFileSync(
    "./SimpleStorage_sol_SimpleStorage.bin",
    "utf8"
  );
  
  const contractFactory = new ethers.ContractFactory(abi, binary, wallet);
  const contract = await contractFactory.deploy();
    // you can also set some overrides in the deploy function input like 
    // {gasLimit: 10}
  await contract.waitForDeployment();
} 

main();
```

ALTERNATIVE (just useful to understand what happens under the hood)
Once you have set up the Wallet object, 
you can also deploy yourself by specifying and signing the transaction:
```javascript
const nonce = await wallet.getTransactionCount();
const tx = {
	nonce: nonce, // unique number that is the num of blocks
	gasPrice: 200000000
	gasLimit: 2000000000,
	to: null, // no destination address as we are deploying a contract
	value: 0,  // 0 wei sent with the transaction
	data: "0xfhu9h02"  // this is '0x' + the binary content of the compiled .bin
	chainId: 5777
};

await sentTx.waitForDeployment();  //wait for deployment block creation
```

CALL CONTRACT FUNCTIONS WITH ETHER:
after setting up the contract as previously shown
```javascript
// retrieve is a function exposed by the contract
const currentFavNumber = await contract.retrieve();
// numbers are exposed as BigNumber type, not native numbers for JS
const stringNum = currentFavNumber.toString();

// 7 passed as string for the same reason as before, 'store' is another fn
const response = await contract.store("7");
// every transaction that uses gas needs to wait for the block to be crated
await response.wait(1);
```

DEPLOY ON TESTNET
You can test your app in an actual blockchain distributed testnet, using Alchemy website.
Then you will need to use the provided RPC URL and the private key will be from your metamask wallet.
On etherscan.io you can **verify and publish** your contract.

