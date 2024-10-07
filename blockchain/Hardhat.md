Development environment for JS and WEB3 (dev dependency)
`yarn add hardhat`
then 
`yarn hardhat` to init the project
hardhat lets you do things like:
- `yarn hardhat compile` -> compile contracts (outputs to artifacts folder)
- `yarn hardhat run <scriptname>`  -> run a js script

Then you can write the deployment script (using ether wrapped inside hardhat):
```javascript
const { ethers } = require("hardhat");

async function main() {
  const simpleStorageFactory = await ethers.getContractFactory("SimpleStorage");
  const simpleStorage = await simpleStorageFactory.deploy();
  await simpleStorage.waitForDeployment();
}
main();
```
You can compile using multiple versions:
```javascript
module.exports = {
  solidity: {
    compilers: [{ version: "0.8.7" }, { version: "0.6.0" }],
  },
```

Hardhat run with a built in Hardhat network (similar to Ganache)
This network will be destroyed after the run.
In order to keep a localhost network that keeps running you need to enter the command:
`yarn run node`

and then add the network detail in the hardhat.config:
```javascript
module.exports = {
  solidity: "0.8.8",
  networks: {
    localhost: {
      url: "http://localhost:8545",
      chainId: 31337
    },
  },
};
```

and then run the script specifying `--network localhost`

In order to specify a real test network (need some eth inside) you need to add the `--network <name>` flag to the hardhat run command and you need to add the network info in the *hardhat.config*:
```javascript
module.exports = {
  solidity: "0.8.8",
  networks: {
    sepolia: {
      url: process.env.SEPOLIA_RPC_URL,
      account: [process.env.PRIVATE_KEY],
      chainId: 11155111
    },
  },
};
```

In order to programmatically verify the contract after deployment we can add an hardhat plugin:
`yarn add --dev @nomiclabs/hardhat-etherscan`
Then you'll need to create an account on etherscan and create an apikey and add it to the *hardhat.config*:
```javascript
require("@nomiclabs/hardhat-etherscan");

module.exports: {
	etherscan: {
		apiKey: "..."
	}
}
```

then you can verify programmatically:
```javascript
const { ethers, run } = require("hardhat");
require("@nomiclabs/hardhat-etherscan");

async function verify(contractAddress, args) {
  try {
    await run("verify:verify", {
      address: contractAddress,
      constructorArguments: args,
    });
  } catch (e) {
    console.log(e);
  }
}

//we will call the verify after waiting some blocks confirmation
await myContract.waitForDeployment();
```

TASK
they are generally useful for plugins 
In hardhat you can create custom task that you can then execute in the command line
generally you create a tasks folder with js files for each task.
```javascript
const {test} = require("hardhat/config");

task("block_number", "Prints the block number").setAction(
	async (args, hre) => {
		//hre: hardhat runtime environment
		const num = await hre.ethers.provider.getBlockNumber();
		console.log(num);
	}
);
```

TESTS
by default it uses Mocha for unit test
`yarn hardat test`
`yarn hardhat test --grep "name"`  to run only a test

in VSCode if you have a debugger breakpoint and a debugger terminal, running the command will automatically attach the debugger.

TESING GAS COST
add the package `yarn add hardhat-gas-reporter`
end this bit of configuration in the *hardhat.config*:
```javascript
gasReporter: {
	enabled: true
}
```
Now the tests output gas prices:
![[Pasted image 20240328165729.png]]

You can also get a real time currency value, you need to sign up to coinmarketcap and create the private key
```javascript
gasReporter: {
	enabled: true,
	currency: "USD",
	coinmarketcap: "key...",
	token: "MATIC"  //this is used to choose the blockchain by its native token                          (prices will change)
}
```

COVERAGE
add the package `yarn add --dev solidity-coverage`
then run `yarn hardhat coverage` that generates the coverage.json

For typescript there is a typechain package that after install can generate automatically types of the contracts so that we can work with them in TS (run `yarn hardhat typechain` which outputs on *./typechain-types*)

DEPENDENCIES
All the dependencies that we were directly importing in remix (import "@chainlink/something")
Are still correctbut we need to add them to the package.json dependencies as well


MULTIPLE DEPLOYMENTS
use hardhat plugin `yarn add hardhat-deploy`
and add the import/require in the hardhat config file

and another command:
`yarn add --dev @nomiclabs/hardhat-ethers@npm:hardhat-deploy-ethers ethers`
that is overriding hardhat-ethers with hardhat-deploy-ethers

Now all the scripts in the deploy folder will be executed (in alphabetical order, it's best practice to prepend them with a number) when running `hardhat deploy`
and the script are now developed differently:
```javascript
module.exports = async (hre) => {
// custom logic here
};
```
inside the deployment script you can search for another deployment like so:
```javascript
await hre.deployments.get("MockV3Aggregator");
```

UNIT TEST
To unit test your deployments you can create all of them in the before each:
```javascript
const { deployments, getNamedAccounts, ethers } = require("hardhat");

describe("Fund me test", async () => {
  let fundMe;
  beforeEach(async () => {
    const { deployer } = await getNamedAccounts();
    await deployments.fixture(["all"]);
    fundMe = await ethers.getContract("FundMe", deployer);
  });
});```
In the hardhat project 'chai' is actually overridden by Waffle, it allows to expect transactions to fail:
```javascript
const {expect} = require("chai");

await expect(fundMe.fund()).to.be.reverted;
```
You can also expect something to emit a log:
```javascript
const {expect} = require("chai");

await expect(fundMe.fund()).to.emit(fundMe, "EventName");
```
You can simulate the call of a method in a contract without creating a transction and everything, just like calling a normal method in another JS object, using callStatic:
```javascript
const result = await fundMe.methodName.staticCall();  //methodName is a public mthod in the smart contract
```

NAMED ACCOUNTS
in the hardhat config you can specify different users that will be used (by specifying also their private key)

```javascript
module.exports: {
	...,
	namedAccounts: {
		deployer: {   // the name of the account is up to you
			default: 0,  // this means that this will be the account in position 0
		},
		user: {
			default: 1,
		}
	}
}
```

A trick to work with multiple chains is to create a helper-hardhat.config file that have maps from chainId to the required properties.

MOCK
if you need another contract to interact with in your localhost, you will need to deploy a mock of it in order to test your own contract.
You can add a deploy script: *00-deploy-mock.js* and deploy all the mocks there. The mock is an actual solidity contract that you have to implement.


then you can tag the different deploy scripts in order to run only a subset of them if needed,
for example:
```javascript
//into 01-deploy.js
module.export.tags = ["all"];
```

```javascript
//into 00-deploy-mocks.js
module.exports.tags = ["all", "mock"];
```
and then run `yarn hardhat deploy --tags mock` to deploy only the mocks

STAGING TESTS
those tests are run in a test net.
you want to make sure that these tests are run in the test net:
```javascript
const { getNamedAccounts, ethers, network } = require("hardhat");
const { developmentChains } = require("../../helper-hardhat.config");

developmentChains.includes(network.name)
  ? describe.skip
  : describe("FundMe staging tests", async () => {
      let fundMe, deployer;
      const sendValue = ethers.utils.parseEther("1");
      beforeEach(async () => {
        deployer = (await getNamedAccounts()).deployer;
        fundMe = await ethers.getContract("FaundMe", deployer);
      });

      it("test here", () => {
        //test
      });
    });
```

NETWORK METHODS
HardHat support Network methods that allow you to interact directly with the EVM in the tests or wherever you need it.
example:
```javascript
          await network.provider.send("evm_increaseTime", [300]);
          await network.provider.send("evm_mine", []);

```

ACCESS EVENTS:
In JS you can access emitted events data starting from the *transactionReceipt* after performing a transaction:
```javascript
txReceipt.logs[0].args   //both indexed and non-indexed
```


FORKING MAINNET
A nice way to test your contracts with hardhat is to fork a net (it can be any net, you will download it locally and create a local fork that will not interact with the real net) A good advantage is that you get automatically everything (also contracts) that is deployed in the main net.
in hardhat.config.js:
```javascript
networks: {
	hardhat: {
		chainId: 31337,
		forking: {
			url: MAINNET_RPC_URL,  //the url of the fork (that you can easily create with alchemy website)
		},
	},
}
```