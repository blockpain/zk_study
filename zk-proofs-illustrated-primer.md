https://blog.cryptographyengineering.com/2014/11/27/zero-knowledge-proofs-illustrated-primer/

"Most real systems implement this 'proof' in the absolute worst possible way. The client simple transmits the original password to the server, which re-computes the password hash and comptes it to the stored vale. the problem here is obvious: at the conclusion of the protocol, the server has learned my cleartext password. Modern password hygiene now involves a good deal of praying that servers arent compromised."

What Goldwasser, Micali and Rackoff proposed was a new hope for conducting such proofs. If fully realized, zero knowledge proofs would allow us to prove statements like the one above, while provably revealing no information beyond the single bit of information corresponding to ‘this statement is true’.

Goldwasser, Micali and Rackoff proposed three following properties that every zero-knowledge protocol must satisfy. Stated informally, they are:

    Completeness. If Google is telling the truth, then they will eventually convince me (at least with high probability).
    Soundness. Google can only convince me if they’re actually telling the truth.
    Zero-knowledgeness. (Yes it’s really called this.) I don’t learn anything else about Google’s solution.

We’ve already discussed the argument for completeness. The protocol will eventually convince me (with a negligible error probability), provided we run it enough times. Soundness is also pretty easy to show here. If Google ever tries to cheat me, I will detect their treachery with overwhelming probability.

A commitment scheme allows one party to ‘commit’ to a given message while keeping it secret, and then later ‘open’ the resulting commitment to reveal what’s inside. They can be built out of various ingredients, including (strong) cryptographic hash functions.


Part 2
---------

https://blog.cryptographyengineering.com/2017/01/21/zero-knowledge-proofs-an-illustrated-primer-part-2/

ZK-Proofs are an interaction between two computer programs - respectively called a Prover and a Verifier - where the Prover works to convince the Verifier that some mathematical statment is true. 

There are three properties that nay zero knowledge proof must sarisgy:

    **Completeness**: If the prover is honest, then she will eventually concice the Verifier
    **Soundness**: The prover can only convince the Verifier if the statment is true.
    **Zero-Knolwedgeness**: The Verifier learns no infonrmation beyond the fact that the statment is true

Goldwasser, Micali, and Rackoff argued that a protocol can be proven zeroknowledge if for every possible Verifier, you can demonstate the existaence of an algorithm called a 'Simulator', and shows that this algorithm has some very special properties. 

From a purely mechanical perspecitve, the Simularotr is like a special kind of Prover, but unlike a real Prover - Which starts with some speicial knowledge that allows it to prove the truth of a statment - the Simulator gets no special knowledge at all. The Simulators must be able to 'fool' every Verifier into believing that the satment is true, while also producing a transcript that's statisctially identical to the output of a real Prover




