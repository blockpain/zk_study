https://medium.com/fcats-blockchain-incubator/how-zk-rollups-work-8ac4d7155b0e

How Zk-Rollups Work 
--------------------

Zk-rollups, like optimisitic rollups, store all addresses and respective balaces in a merkle tree. The root of the "balance tree" is stroed in an on-chain smart contract.

When a batch is posted on-chain, a new merkle root is proposed, which reflects the updated merkle tree, with updated balances from all the fransfers that are included in the batch. The smart contract will verify a snark proof that's posed with the batch, and if it checks out, the new merkle root is accepted, and becomes the canonical state root of the contract.  

Deposits
--------

When a deposit is made toa rollup, tokens are sfent to the rollup contract. The rollup contract adds the tokens to apending deposit queue. At a certain point, a coordinator will take a number of deposits and add them to the rollup, whcih basically involves including them in the merkle tree of  balances

Walkthrough
-----------
When a deposit is made to the smart contract, the smart contract takes al the relevant information (publick key, amount, token type) and creates a hash. This hash is added to the end of the deposit queue. Next the contract recursively hashes the last two deposit hashes in the queue,iteratively building up a merkle tree of deposit hashes. This merkle tree of deposit hashes becomes a subtree of the rollup balances tree. 

Lets walk through an example:

A deposit is made to the rollup contract so thta the deposit queue now has one deposit:

```
Hash(pubkey, amount, token type) = 0x1234abcd…
```

Another subsequent deposit is made at a later stage:

```
Hash(pubkey, amount, token type) = 0x9876fedc…
```

The deposit queue now looks like this:

```
[0x1234abcd, 0x9876fedc]
```

However, now that we have an even number of deposits, the second deposit transaction continues the process by hashing the last two element of the queue:

```
Hash([0x1234abcd, 0x9876fedc]) = 0x6663333
```

Now the deposit queue looks like this:

```
[0x6663333]
```

Processing Deposits
-------------------

The first thing to understand before we can go into hwo deposts are processed, is that all deposits and balances in the rollup are stored in a "sparse-merkle-tree". this is essentially a merkle tree of a fixed size that is pre-initialized with all zeros, (or the equivalent hashes).

To process the pending deposits, a coordingator will take the first element of the pending deposits queue, and will insert it into the rollups sparse balance tree at the correct height. This will of course result in a new merkle root for the balance tree, whcih the coordinator posts to the rollup contract.

In order to accept this new balance tree merkle root, the contract must be satisfied that the coordinator is inserting the deposit subtree into a previously empty part of the balance tree. To satisfy the smart contract of this, the coordinator also posts a merkle proof of an empty node at the corresponding level of the balance tree, that will be replaced by the root of the deposit subtree. It looks a bit like this:

Recall that at this point the contract has everything it needs to verify that deposit subtree has been incorporated into the rollup correctly, namely:

    It has the sub tree height, which in this example is 2.
    It knows what an empty node at a height of 2 is, because it has a cache of empty node hashes for each level of the tree.
    It has just been given a merkle proof for this empty node so that it can verify that it is part of the current balance tree.
    It has the root of the deposit subtree (the first element in the deposit queue).

Zero-Knowledge Proof
--------------------
The coordinator will create a proof that all the transactions were carried out correctly. Recall that each coordinator maintains a local database of deposits, that builds up from subscribing to the deposit events emitted by the smart contract. When a transaction is received, the coordingator verifires it inteh normal way by checking against the dtails in the database, it verifies:

    The transaction is correct and corresponds to the depositor’s public key.
    The depositor’s account exists in the balance tree.
    The amount being transferred is not greater than the depositor’s balance.
    The receiver account exists in the balance tree.
    Both the sender account and receiver account are of the same token type.
    The sender’s nonce is correct.
    There are no overflows / underflows etc.

Once these things are checked, it is added to the queue. Once a number of transations are in the qeuue, the coordinator will decide the create a batch. to do this, the coorinator compiles a bunch of inputs for the zk=proof circuit to compile into a proof. This involves:

    Creating a merkle tree of all the transactions in the batch, which is padded with dummy transactions up to the size required by the circuit, and each with a merkle proof.
    Creating a collection of merkle proofs for each sender and receiver from all transactions in order to prove account existence.
    Creating a collection of intermediate roots which are derived from updating the balance tree root after updating the sender and receiver accounts in each transaction.

This last part is what allows the smart contract to be satisfied that the transactions in the batch were applied correctly. Let me explain.


