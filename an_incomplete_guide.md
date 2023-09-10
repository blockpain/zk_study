https://vitalik.ca/general/2021/01/05/rollup.html

An Incomplete Guide to Rollups
------------------------------

"Rollups are poised to be the key scalability solution for Ethereum for the foreseeable future. But what exactly is this technology, what can you expect from it and how will you be able to use it. This pose will attempt to answer some of those key questions"

There are two ways to scale a blockchain ecosystem. First, you can make the blockchain itself has a higher transaction capacity. Main challenge is that blockchains with "bigger blocks" will be inherently more difficult to verify and likely to become more centralized. to avoid such risks, devs can either increase the efficiency of client software or, more sustainably, use techniques such as sharding to allow the work of building and verifying the chain to be split up across many nodes; the effort known as "eth2" is currently building this upgrade to Ethereum. 

**Second** you can change the way that you use the blockchain. Instead of putting all activity on the blockchain directly, users perform the bulk of their activity off-chain in a "layer2" protocol. there is a smart contract on-chain, which only has two taks: processing deposts and withdrawals, and verifying proofs that everything happening off-chain is following the rules. 
