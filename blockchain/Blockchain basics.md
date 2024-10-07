## Create a Blockchain
Start with a Communal ledger: an append only data structure that is public and anyone can add new lines

How do we ensure that the new transactions are valid?
with digital signature: every transaction need to be signed
How? -> every node has a public and private key and sign transaction using the private key (message + private key are used to sign, message contains also unique number so that we cannot craft duplicates transactions)
Anyone (only one is needed) can sign a transaction, and anyone can verify the signature just using the public key of the signer.

In order to avoid people going in debt, only positive wallet are allowed (they cannot go under 0)

Who host the ledger?
To avoid giving trust to someone we replicate the ledger in each node's PC.

At this point how do you reach consensus between each copy?
Imagine a node listening for new transaction, how do you know if the others nodes have the same data in the same order?

The solution that bitcoin uses to solve this issue is to:
- trust the ledger with the longest list of transactions
- make the process of adding a transaction computationally hard so that trying to fraud the system became computationally harder and harder as the ledger grows -> PROOF OF WORK

Proof Of Work
The computational riddle need for bitcoin is to find a nonce that if appended to the ledger and hashed will give a certain property to the result (like the first 30values are zeroes)
This method requires and proof that the node has undergone computational effort, and can be verified instantly without needing computational work again.

The proof of work is not applied at every transaction but the concept of block is introduced to group a set of transactions.
How do we ensure that previous block are not tampered?
We keep a reference of the previous block in every block (creating a chain of blocks), so that if the previous block is tampered, its hash changes and also the hash of the current block changes.

The node that generates the proof of work is rewarded with a special transaction (the only one that is not signed) which gives a some native coins to the miner node.

Whenever a node listens to different nodes it will trust the longest copy of the chain because more work have been put in place in that version.

If you try to attack this system by crafting a transaction and sending it only to a node,
you need to find the proof of work before any other node on the chain to validate the block.
(at this point this is really feasible), but whenever a new node is added, and other nodes are adding nodes, the attacker would need to beat all the other participants in computing the proof of work each time a new block is added. This gets less probable at each new block added.
In order to perform such attack successfully the attacker would need to control at least 50% of the network, it is in fact called 50% attack.
## General Concepts
Bitcoin is the fist protocol designed to use a blockchain as a network.
Bitcoin is the ledger itself and the currency inside it.
It is limited in amount and controlled by an algorithm, that makes it a way of storing value.
Ethereum comes after with the possibility to wite smart contracts over the blockchaing unlocking the possibility to build DApps.
Smart contract: set of instructions executed in a decentralized way without the need for a centralized third party. (executed on smart contract platforms like Ethereum).

Actually the bitcoin protocol has smart contracts, but they are designed Turing incomplete to limit the bitcoin to be store of value.

Oracle: devices that gives data to blockchain, they are decentralized as well. (they bring the off-chain data to the network). When off-chain components are used in a smart contract, we call it Hybrid smart contract.

Web3: the idea that smart contracts and decentralized applications are the next generation of the web (web1, static content, we2: web with access, permissions and dynamic content). The web3 is permissionless in the sense that the constraint are given by decentralized protocols, and it contains dynamic content like web2. The protocols themselves are also owned partially by the user of the web.

Smart contracts are agreements deployed in a decentralized blockchain, it is immutable (huge security advantage over web2), it automatically executes and transparency. Smart contracts are trust minimized agreement: agreement that not need trust because of the way they are designed.
The smart contracts are run by people that are giving computational power: NODE OPERATORS

DeFi: gives users the access to the markets in a decentralized way.
DAO: decentralized, autonomous organization: organization governed by smart contracts.
NFT: non fungible tokens, unique assets stored in chain.


Use a testnet with fake money, and use a faucet to claim fake tokens on those testnets

GAS fees:
a blockchain is run by nodes, every transaction has a transaction fee that gives money to the people that are running those nodes. (transaction fee = num_of_gas * single_gas_fee) .
The number of gas is dependent on the computational cost of the operation executed.

Gas fees changes during time based on how much operations are active in the blockchain at the moment.

**BLOCK**
described by:
- block number
- nonce
- data (a list of transaction)
- previous block hash
all of those info are grouped together and sent to the hash algorithm (Keccak256 for ethereum).

Not all the hashes are valid, only the ones that respect a certain criteria, for example only hashes that start with 4 zeroes are valid, the nonce needs to be computed in order to make the hash fit the requirements. This process of finding the proper nonce (through brute-forcing) is called mining. (the specific requirements that the hash has to respect changes for each blockchain)

A blockchain is a chain of linked blocks (the link is the hash of the previous block).
The genesis block has '0' as pointer because it has no previous block.

Every block as a valid state, only if the hash respect the requirements, if any data in the blockchain is corrupted then the block is invalid, and his hash changes, by consequence also the hashes of the next blocks are changing and those blocks are becoming invalidated as well, up until the last one.

All the blocks in the chain are replicated in all the peers in the network. That's why it's distributed and decentralized. If any peer has a different hash for the last block they are going to be kicked out. The majority of peers decides which data is right and which is not.

**KEYS**
there is a couple of keys (private/public) that are linked with Elliptic curve encryption.
You can easily create the public key from the private one but not the opposite.
The private key is used by you to sign, the public one is used to verify that you are the one doing operations.
Each transaction in a block is signed by the private key of your address. (you can see your private key in metamask) While the public key public and it generates the addressId (hashing the public key and extracting the last 20 bytes).

Whenever you send a transaction you burn ETH, or whatever is the main coin of the blockchain.
burning ETH removes the token from the chain, it is a way to avoid inflation.
There is a base fee that you give to the nodes.

Block confirmations: the number of blocks mined after the one we are looking at.
Some operations required n block confirmation to be sure that the chain is valid

**CONSENSUS**
Is the mechanism used to reach an agreement in the decentralized system.
**Nakamoto consensus**: The longest chain is considered the valid one + Proof of work.

**Sybil attack**: people trying to create multiple nodes over the network while they are only one entity.
**51% attack**: multiple nodes collaborating to reach the 51% of the blockchain and becoming the rulers of the chain.
**PROOF OF WORK**: it's a way to define who is the author of the block, it is mining, it is on purpose computationally expensive. The first node that is resolving the proof of work is getting the gas fees as reward (+ block awards that is halving in bitcoin).
https://faucets.chain.link/E: it's an ecological alternative to proof of stake, nodes are staking collateral to the network, if they misbehave they lose the collaterals (miners are called validators). The validators are selected randomly by an external decentralized unit. no computation needed! only one selected node is actually mining the nonce.
ETH 2.0 is moving to proof of stake.

**PROOK OF HISTORY**
https://medium.com/solana-labs/proof-of-history-explained-by-a-water-clock-e682183417b8
Transactions timestamped with PoH are then processed using a Proof of Stake (PoS)-based consensus algorithm, Tower BFT, in Solana’s case. Validators stake SOL (Solana’s token) to participate, earning rewards for securing the network and validating transactions. Tower BFT, with the help of PoH’s timekeeping, quickly achieves consensus, allowing Solana to handle thousands of transactions per second​​.


**SMART CONTRACT CONNECTIVITY PROBLEM**: the blockchain needs to be deterministic, in order to ensure consensus between peers. For this reason a blockchain contract cannot make API calls outside or call random generating functions, this causes the blockchain to be isolated from the world.
solution: ORACLES: a proxy to the outer world, that is returning the same value for each peer in the chain -> no more problem with consensus.
But the ORACLE is a single point of failure as opposed to the decentralized nature of the blockchain. Solution used by ChainLink: DATA FEEDS: use a bunch of different nodes/oracles and apply a consensus algorithm on top of their outcomes, then deploy this trusted answer as a contract in the blockchain.

**VRF** (verified random functions) are oracles that send random values to the chain.
**Keepers**: they are like chronjobs that are triggered based on conditions on external events, they are executed over distributed nodes and they send data to on-chain contracts.