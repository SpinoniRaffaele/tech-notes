Layer one: any base layer blockchain implementation (bitcoin, ethereum, avalanche)
Layer two: any application added on top of the blockchain (chainlink, arbitrum).
	Layer two solutions are built to solve the scalability issues of the layer one network (too  many transaction on ETH causes network congestion, slow transaction times, high gas fees).
	They are built on top of the layer one and they rely on the security of the layer one but they speed up transactions, they prioritize speed and they send the result to the layer 1 only for the final approval.
	There are two types of L2 networks:
	- sidechains: independent blockchains with different native tokens and consensus mechanism, they use a two-way bridge to communicate to the L1 chain.
	- rollups: they bundle a batch of transactions and send them as a single piece to the L1 chain.
	- plasma: a separate chain that is anchored to the main net and uses fraud proofs.

### Roll ups
**Optimistic RollUps**
They offer 10-100x improvements in scalability without sacrifices in security.
Optimistic rollups are considered “optimistic” because they assume off-chain transactions are valid and don't publish proofs of validity for transaction batches posted on-chain. It's the L1 responsibility to then ensure security. When submitting the batch to Ethereum (*assertion*) there is a time window during which anyone can challenge the rollup transaction by computing a fraud proof.
If a fraud proof is found, the protocol updates the transaction state accordingly and the sequencer that included the incorrect transaction receive a penalty. If not fraud fproof is found in the time window -> transactions added to ethereum.
It is composed of two components:
- on-chain contracts: contracts that store rollup blocks on the L1 chain
- off-chain VM: where the actual transaction state is computed and stored, it os made out of multiple nods called 'operators' (validator, aggregator). The state is organised as a Merkle tree having ah hash as root, only batches that has an initial state matching this root can modify the state. This VM is compatible with the EVM (at bytecode level) so that DApps developed for L1 can be easily ported on L2.
Anyone can be a validator of the rollups, the system uses a system similat ro proof of stake to enforce honesty.
A node called 'operator' bundles the off-chain transactions and sends them to the L1 deployed smart contract using the calldata part of the contract (non-modifiable gas-cheap area).

Fraud proving:
- single-round fraud proving: During the fraud proof computation, if the state is incorrect, the operator that published the state get slashed (loose the resources staked), the transactions then need to be re-executed on L1 (gas cost)
- multi-round fraud proving: after a challenger has challenged an assertion, the asserter is required to divide the assertion in two equal halves, then the challenger have to say which of the two section to challenge, and this operations are repeated until only one step of execution is isolated, at this point only this step is sent over the L1 contract to resolve the dispute. This type of fraud proving is more efficient than the single-round because it minimizes the work on L1 chain

Security property of optimistic rollups: the validity of the chain relies on the existence of only _one_ honest node. The honest node can advance the chain correctly by either posting valid assertions or disputing invalid assertions.

The waiting time for a withdrawal from a L2 (to a L1) is the time window of fraud checking. it is around one week, in order to speed up the request users can use a liquidity provider that assumes ownership of the pending withdrawal and pays the user (in exchange for a fee). The liquidity provider can itself verify the validity of the withdrawal request before paying.

**Zero knowledge rollups**
This kind of rollups produce a validity proof, so that once they submit to the L1, no additional checks needs to be performed and they only need to post the new state on-chain (with calldata) without giving information about the transactions (zero knowledge)
Moving funds from the ZK-rollup to Ethereum is fast (as opposite as the time window of fraud proof for the optimistic rollout).
It is composed of two components:
- on-chain contracts: one used to store rollup blocks on the L1, one verifier contract that verifies the ZK proofs submitted.
- off-chain VM: executes transactions and stores them. The state is represented as a Merkle tree.

In general rollups there is the concept of 'supernode' which execute transactions and produces batches (it reduces the decentralization and increase risks).
As a security measure, rollups allow users to submit transactions directly to the rollup contract on Mainnet if they think they are being censored by the operator.

ZK-rollups use validator to process transactions, it uses proof-of-stake to ensure honesty.

When updating the state, the rollups send the new Merkle tree root, being the representation of the new state, and sole the old merkle tree root.
In order to prove that the new state is the correct one, a validity proof is provided. It is called ZK-SNARK : zero knowledge succinct non-interactive Argument of Knowledge. Or ZK-STARK: zero knowledge  scalable transparent argument of knowledge.

ZK rollups are not readly compatible with the Ethereum VM (ongoing efforts are moving towards the creation of a zkEVM) a zkEVM works as an EVM, but at every steps it creates a zk proof to verify correctness.
With zk-rollups there is not need for honesty in the actors, the proof is given in a trustless cryptographic mechanism. Producing validity proofs however requires specialized hardware whici encourages centralized control

### Side Chain
They are a completely separate blockchain that uses two-way bridges to connect to L1 mainnet.
Unlike rollups they do not post state changes to the mainnet so they don't inherit the L1 security.
Side chains use their own consensus mechanism which can vary a lot from one to another.
Side chain achieve better performances by using faster block times (time it takes to produce new blocks) and bigger block sizes (ethereum has thight limits on both). This has critical implication for decentralization and security. Bigger and faster block created means that the hardware required to be a validator is much more expensive, thus the decentralization decrease.
Sidechains are EVM compatible.

### Plasma chain
Like side chains, Plasma is a separate chain anchored to mainnet, they are essentially smaller copies of ethereum with their own mechanism for block validation. They use a bridge to communicate with the mainnet like sidechain, but they rely partially on Ethereum mainnet security (better than sidechains, but still worse than L2 on top of mainnet).
Plasma can be used for smaller transaction where trust is not so important, a Plasma chain most often use a single "operator" to manage the ordering and execution of transactions. With just one entity verifying transactions, processing times on a plasma chain are faster than Ethereum Mainnet.

Plasma uses a master contract running on ethereum to process users entries and exits, this contract act as bridge, entering the chain is an instant conversion, while withdrawal is a long process due to thefraud proof mechanism (same as optimistic rollups)
