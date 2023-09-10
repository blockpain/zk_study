https://vitalik.ca/general/2021/01/05/rollup.html

An Incomplete Guide to Rollups
------------------------------

"Rollups are poised to be the key scalability solution for Ethereum for the foreseeable future. But what exactly is this technology, what can you expect from it and how will you be able to use it. This pose will attempt to answer some of those key questions"

There are two ways to scale a blockchain ecosystem. First, you can make the blockchain itself has a higher transaction capacity. Main challenge is that blockchains with "bigger blocks" will be inherently more difficult to verify and likely to become more centralized. to avoid such risks, devs can either increase the efficiency of client software or, more sustainably, use techniques such as sharding to allow the work of building and verifying the chain to be split up across many nodes; the effort known as "eth2" is currently building this upgrade to Ethereum. 

**Second** you can change the way that you use the blockchain. Instead of putting all activity on the blockchain directly, users perform the bulk of their activity off-chain in a "layer2" protocol. there is a smart contract on-chain, which only has two taks: processing deposts and withdrawals, and verifying proofs that everything happening off-chain is following the rules.

STate channels vs Plasma vs Rollups
-----------------------------------

The three major types of l2 scalaing are state channels, Plasma, and rollups. They are three differenet paradigms, with different strengths and weaknesses, and at this point we are fairly confident that all l2 scaling falls into these three categories

Channels
----------

Imagine that Alice is offering an internet connectoin to Bob, in exchange for Bob paying her $0.001 per magabyte. Instead of making a transaction for ecah payment, Alice and Bob use the follwing layer-2 scheme

First, Bob puts $1 (or some ETH or stablecoin equivalent) into a smart contract. To make his first payment to Alice, Bob signs a "ticket" (an off-chain message), that simply says "$0.001", and sends it to Alice. To make his second payment, Bob would sign another ticket that says "$0.002", and send it to Alice. And so on and so forth for as many payments as needed. When Alice and Bob are done transacting, Alice can publish the highest-value ticket to chain, wrapped in another signature from herself. The smart contract verifies Alice and Bob's signatures, pays Alice the amount on Bob's ticket and returns the rest to Bob. If Alice is unwilling to close the channel (due to malice or technical failure), Bob can initiate a withdrawal period (eg. 7 days); if Alice does not provide a ticket within that time, then Bob gets all his money back.

Channels cannot be used to send funds off-chain to people who are not yet participants. Channels cannot be used to represent objects that do not have a clear logical owner (eg. Uniswap). And channels, especially if used to do things more complex than simple recurring payments, require a large amount of capital to be locked up.

Rollups
--------

Plasma and channels are "full" L2 schemes (they try to move both data and computation off-chain. Rollups are "hybrid" l2 schemes, moving computatoin (and state storage) off-chain, but keep some data per transactoin on-chain. To improve efficiency, they use a whole host of fancy compression tricks to replace data with computation wherever possible. The result is a system where scalability is still limited by the data bandwidth of the underlying blockchain, but at a very favorable ratio: whereas an Ethereum base-layer ERC20 token transfer costs ~45000 gas, an ERC20 token transfer in a rollup takes up 16 bytes of on-chain space and costs under 300 gas.

How exactly does a rollup work
------------------------------

There is a smart contract on-chain which maintains a state root: the Merkle root of the state of the rollup (meaning, account balances, contract code, etc, that are "inside" the rollup).

Anyone can publish a batch, a collection of tx in a highly compressed form together with the previous state root and the new state root. 
