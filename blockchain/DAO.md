Decentralized Autonomous Organisation: a group controlled completely autonomously by a set of contracts.

A system in which users can vote on what the system should do, the voting system need to be immutable transparent and decentralized: this process is decentralized governance.

You can check a real governance tab in Compound.
To create a proposal of change, there is a smart contract with which you can interact and send the proposal (creation), then after a time window the proposal becomes active and other people can vote for/against it using another contract. After the votes have reached majority, the proposal gets in queued state. After a while it gets Executed. Instead of majority the proposal can become Failed.

How do you make people vote?
One option is to use a token (ERC20) like the DAOs token, but this would cause the problem of the richest voter being more influential.
Other option: SKIN IN THE GAME. With this system every vote is recorded and if the vote lead to a bad decision, than the voters token are slashed. But what is a bad decision? not clear.
Last option: one wallet corresponds to only one vote, but it's vulnerable from sybil attack (one participant with multiple wallet, voting multiple times).

Voting can happen on-chain -> gas fees are the main cons of this method. But it's simpler.

An alternative is to vote off-chain, by sending the transaction to a decentralized DB and then send the result on-chain.

There is the issue of: who is responsible for what the DAO is doing? this is a grey area and every state can put different laws.