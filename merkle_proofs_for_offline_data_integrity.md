https://ethereum.org/en/developers/tutorials/merkle-proofs-for-offline-data-integrity/#main-content

Merkle proofs for offline data integrity
----------------------------------------

Intro
-----
Ideally we'd like to store everything in Eth storage, which is stored across thousands of computers and has extremely high availability (data cannot be censored) and integrity (the data cannot be modified in an unauthorized manner), but storing a 32 byte work typically costs 20k gas. 

How Does it Work
----------------
In theory we could just sotre the hash of the data on chain, and send all the dfata in transactions that require it. However, this is still too expensive. The solution is to repeatedly hash different subsets of the data, so for the data that you don't need to send you can just send a hash. You do this using a Merkle tree, a tree data structure where each node is a hash of the nodes below it:
```
                            +----------------+
                            |      ROOT      |
                            +----------------+
                             /              \
                            /                \
                      +--------------+  +--------------+
                      |   Hash(0-1)  |  |  Hash(2-3)   |
                      +--------------+  +--------------+
                       //       \\          //      \\
                      //         \\        //        \\
                    +-----+   +-----+  +-----+     +-----+
                    | H0  |   | H1  |  | H2  |     | H3  |
                    +-----+   +-----+  +-----+     +-----+
                      ||        ||       ||          ||   
                      ||        ||       ||          ||   
                    +-----+   +-----+  +-----+     +-----+
                    |Data0|   |Data1|  |Data2|     |Data3|
                    +-----+   +-----+  +-----+     +-----+
```

The root hash is the only part that needs to be stored on chain. To prove a certain value, you provide all the hashses that need to be combined with it to obtain the root. For example, to proce Data1, you include H1 and  H(2-3). 

Implementation
--------------

The following code contains 5 functions:
    **getRoot()**; which returns the merkleRoot
    **setRoot()**; which takes as input a new uint merkleroot, and sets this value
    **hash()**; which is used to hash a given value
    **pairHash()**; which takes the hash of two given values, `a` and `b`
    **verifyProof()**; which takes as input a uint value, and uint[] `proof`. this takes the pairwise hashes of the given proof arguments to check whether the end result is equivalent to the merkleRoot

important to note is the necessity of an authorization modifier on the `setRoot()` function, even though this is not implmemented in this example.


https://github.com/qbzzt/merkle-proofs-for-offline-data-integrity

```
//SPDX-License-Identifier: Public Domain
pragma solidity ^0.8.0;

import "hardhat/console.sol";


contract MerkleProof {
    uint merkleRoot;

    function getRoot() public view returns (uint) {
      return merkleRoot;
    }

    // Extremely insecure, in production code access to
    // this function MUST BE strictly limited, probably to an
    // owner
    function setRoot(uint _merkleRoot) external {
      merkleRoot = _merkleRoot;
    }   // setRoot

    
    // Same functions as in the JavaScript, just written in Solidity
    // (and no strings needed)
    function hash(uint _a) internal pure returns(uint) {
      return uint(keccak256(abi.encode(_a)));
    }

    function pairHash(uint _a, uint _b) internal pure returns(uint) {
      return hash(hash(_a) ^ hash(_b));
    }

    // Verify a Merkle proof
    function verifyProof(uint _value, uint[] calldata _proof) 
        public view returns (bool) {
      uint temp = _value;
      uint i;
  
      for(i=0; i<_proof.length; i++) {
        temp = pairHash(temp, _proof[i]);
      }

      return temp == merkleRoot;
    }
    
}  // MarkleProof
```

Creating a Merkle Proof
-----------------------
A merkle proof is the values to hash together with the value being provided to get back the Merkle root.


