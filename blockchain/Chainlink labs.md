It's a decentralized computing platform, it provides mainly decentralized oracles that allow access to real world data on-chain. But also other Web3 services.
The smart contract cannot fetch [external data](https://blog.chain.link/understanding-how-data-and-apis-power-next-generation-economies/), because blockchains are like black boxes with no built-in ability to connect with the outside world. The only way to efficiently get data into the blockchain is for a software component called an “oracle” to input it into the blockchain.
If a single, centralized oracle is responsible for inputting the data used to trigger the smart contract, then that oracle has complete control over the smart contract’s outcome. This introduces a serious point of failure known as oracle problem, which puts the entire smart contract at risk.

Why the blockchain cannot just read external data?
How to ensure the data is correct? Every node could potentially submit an incorrect answer.

An oracle is a secure piece of middleware that facilitates communication between blockchains and any off-chain system.

An oracle monitor the blockchain network for incoming requests, it fetches data from the outside word, it generates cryptographic proof, it broadcast the transaction on the blockchain.
The oracle operates both on and off chain simultaneously.

In order to bring determinism to the oracle layer, Chainlink has developed a network of decentralized oracle networks (DONs) (Chainlink is the name of the **framework** used to build decentralized oracle networks). It has some core features:
- open source (allow blockchain community to verify)
- decentralization (no point of failure, guarantees of availablility)
- data signing: oracle nodes sign the data an users can track which node is responsible of what
- service agreements around the quality of data

This ensure high data quality, origin proofs, data validation, data privacy.

Chainlink's DON (decentralized oracle network) is the decentralized network of independent entities (oracles) that collectively retrieve data from multiple sources, aggregate it, and deliver a validated, single data point to the smart contract to trigger its execution, removing any centralized point of failure.![[Pasted image 20240422164058.png]]

# VRF
Chainlink VRF (Verifiable Random Function) is a provably fair and verifiable random number generator (RNG) that enables smart contracts to access random values without compromising security or usability. For each request, Chainlink VRF generates one or more random values and cryptographic proof of how those values were determined. The proof is published and verified onchain before any consuming applications can use it. This process ensures that results cannot be tampered with or manipulated by any single entity including oracle operators, miners, users, or smart contract developers.  Using an advanced cryptographic commitment scheme, Chainlink VRF supplies dApps with tamper-proof on-chain randomness.

# Data Feeds
They provide external data on demand. 
- Price feeds: return the price of assets
- Proof of reserve feeds: provide the reservers of stablecoins, assets or real world assets
- NFT Floor price feeds: provide lowest priced NFT available in a collection.
- ...

Data feeds are composed of multiple components:
- Consumer: onchain application (smart contract) that contacts a proxy contract to retrieve informations.
- Proxy contract: onchain proxyes that point to an Aggregator for a specific data.
- Aggregator contract: an onchain contract that is periodically updated by the oracle network off-chain

# CCIP
Chainlink Cross-chain Interoperability Protocol: allow dApps to transafer assets and information between different blockchains by creating an abstraction layer on top of different blockchains.
They enable easier integration of different chains for a single dApp

Chainlink CCIP provides a single simple interface through which dApps and web3 entrepreneurs can securely meet all their cross-chain needs. You can use CCIP to transfer data, tokens, or both data and tokens across chains.![[ccip-diagram-04_v04.webp]]

# Automation
Automation uses upkeep and triggers to execute on chain jobs when triggered. There are different triggers:
- Time-based trigger: it's like a chron job.
- Custom logic trigger: you can provide a logic that will be evaluated off-chain to determine when to execute the smart contract's function.

Automation nodes form a DON (decentralized oracle network) where the `checkUpkeep` function is executed (off-chain) to determine when upkeeps are eligible to be performed. 
If the execution is needed, it is performed on-chain on your contract that executes the provided `performUpkeep` function.