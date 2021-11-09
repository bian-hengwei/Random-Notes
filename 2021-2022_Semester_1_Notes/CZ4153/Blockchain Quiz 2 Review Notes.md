# Blockchain Quiz 2 Review Notes

[toc]

## Blockchain Security

### Horror Story

-   DAO Hack
    -   2M+ Ether (15%) was emptied
    -   Hard fork of Etherum
    -   Etherum Classic (not hard forked)
-   Parity MultiSig Hack
    -   150K Ether stolen
-   Parity MultiSig Self-destruct
    -   510K Ether Frozen
-   51% Attack
    -   GHash mining pool in 2014 reached 51% on Bitcoin
    -   VergeCoin lost 20M due to 51% in 2018
    -   Bitcoin Gold
    -   Etherum Classic
-   Flash Boys 2.0
    -   Frontrunning attacks on DEX
-   Centralized Exchanges Hacked
    -   Mt. Gox on Bitcoin in 2014
    -   Bitfinex in 2016
    -   Bitnance in 2019
    -   KuCoin (in SG)



### Attacking Blockchains

#### Blockchain architecture:

>   You are as secure as your weakest link

-   Smart Contract (Re-entry, Overflow, Race condition)
-   Runtime (EVM) & Consensus (51%, Collusion, Censorship, Selfish mining, Front running...)
-   P2P Network (BGP hijacking, Eclipse attack)

#### Characteristics of (Permissionless) Blockchain Security

-   Open access
    -   Network
        -   listener/speaker fault equivalence
        -   sybil attacks
    -   Code
-   Immutability
-   Cryptoeconomics
    -   Strategy based on game theoretical optimum given a goal instead of completely following the protocol
    -   Assumptions of rational agents might be broken
    -   Designing an incentive compatible protocol is very challenging and depends on social & economic assumptions
    -   Costs of some attacks might be compensated in surprising ways
-   Less time tested & constantly changing



### Consensus Security

-   51% Forking
    -   Double spend or chain revert/reorganization
-   Selfish Mining
    -   Withholding blocks after finding
    -   Misleading the rest of the network
    -   **Brings down the security threshold down from 51% to**
        -   25% if you can reach 50% of the network faster
        -   33% if you lose propogation races
-   Stubborn Mining
    -   Generalizes selfish mining strategy, find more optimal strategies under different contexts, using Markov Chain Modeling
-   Defense: Selfish Mining & Block Withholding Attacks
    -   Idea: increase cost of block withholding and reward for publishing early
        -   Increase cost: randomly choose one of the competing blocks
        -   Rewards for publishing early: weighed fork-resolving policy (uncle blocks)
-   Trailer: GHOST
    -   Greedy Heaviest-Observerd SubTree
    -   Deal with blockchain scaling problems

#### Frontrunning Attack

-   Idea: frontrun & reordering
-   Categories
    -   Displacement
    -   Insertion
    -   Suppression/block-stuffing
-   To achieve
    -   Be a miner
    -   Set higher gasPrice

#### Defense: Frontrunning Attack

-   Naive solution: simple "commit-and-reveal"
-   Submarine Send
    -   Prepare => Commit => Reveal => Unlock
    -   Key Idea: k-anonymity (hide commit tx as a normal send to random contract address tx)



### Network Layer Security

#### Eclipse Attack

-   Idea: Max 117 incoming and 8 outgoing TCP connections by default
-   Surround a part of the blockchain by attackers
-   Partition the blockchain
-   Double spending may be performed



### Smart Contract Security

-   Overflow & underflow bug
    -   Defense: use SafeMath.sol
-   Reentrancy bug
    -   Defense: finish all internal work before calling the external function
-   MultiSig hack bug
    -   Vulnerable MiltiSig wallet
    -   Defense: add appropriate modifier to avoid public by default visibility





## (Pseudo) Anonymity and Linkability

### Linkability in Bitcoin

-   Bitcoin Transaction Graph
    -   Bitcoin blockchain may be viewed as an evolving acyclic transaction graph $G_t = \{T, E \}$ with $T$: Set of transactions and $E$: Set of unidirectional edges.
-   Bitcoin Address Graph
    -   Transaction graphs leads to an evolving address graph that may have cycles $G_a = \{A, E' \}$ with $A$: Set of addresses and $E'$: Set of unidirectional edges
-   Clustering Addresses to Users
    -   Address graph may be "clustered" to obtain users from addresses
    -   Clustering heuristics may be derived from idiom of use (multiple inputs to a transaction is likely to originate from the same user)
-   Bitcoin User Graph
    -   Address graph may be "clustered" to obtain an evolving user (entity) graph
-   Linking Users to Users
    -   The User Graph may be further “clustered” to obtain linked users (entities).
    -   Clustering heuristics may be derived from idioms of use (output is most likely change address if it is new and is not used regularly)
-   Bitcoin Deanonymization
    -   Bitcoin is susceptible to “deanonymization” analysis due to address linkability



### Unlinkable Transactions

-   Building a transaction alone would make linking input addresses quite trivial

#### Peer-to-Peer Mixing: CoinJoin

-   Idea: instead of building a transaction alone, build one together with other peers
-   Provides unlinkability using standard multi-signature transactions
-   All owners must sign
    -   No loss of coins
-   Main problem: the builder knows the entire mapping

#### Mixing-as-a-Service

-   Some other mixing services:
    -   OnionBC
    -   Bitcoin Fog
    -   BitLaundry
    -   Blockchain.info Send Shared
-   BitLaundry
    -   Deposit input transactions at a single-use address within the mixing network
    -   Specify fresh output addresses to the mixing network to receive transactions
    -   Does not guarantee full unlinkability
    -   Issues:
        -   Mixing service receives inputs and creates outputs
        -   Mixing services knows the entire mapping between all the addresses
    -   Threats:
        -   Mixing service may just steal the money
        -   Mixing service may log or reveal the information on address mapping
    -   CoinJoin  & Mixing
        -   Coinjoin reduces the trust on mixing service and distributes it in a peer-group
        -   It reduces the anonymity level as well; makes it dependent on peer-group size



### Evolution of Anonymity

-	Goals on Anonymity
    -   Unlinkability: Inputs and outputs to a transaction should be unlinkable
    -   Verifiability: Attacker must not be able to steal or destroy transactions
    -   Robustness: Protocol should succeed in presence of malicious parties
    -   AND
    -   Compatible: with the underlying blockchain network and its operation
    -   Efficient: in terms of running the protocol and confirmation timings
    -   Affordable: in terms of transaction fee (or gas) compared to transfers
-	Bitcoin
    -	Found to be pseudonyms
    -	Mixing: make addresses of sender and receiver distant
    -	P2P amd distributed mixing protocols: more anonymity
        -	CoinJoin (P2P, external, DoS)
        -	CoinShuffle (P2P, internal)
        -	TumbleBit (Payment hub for off-chain payment with commitment)
        -	MixCoin (Distributed)
        -	BlindCoin (Distributed, blinded tokens)





## Blockchain Privacy

### Motivation

-   Quest for Privacy
    -   Privacy != Secrecy
    -   Privacy and Auditability/Compliance can coexist
    -   Privacy is having control over choose what to disclose to whom for what
        purpose
    -   Perfect surveillance will stifle social change



### Overview of Privacy Solutions

-   Challenge: maintaining public verifiability while shielding transaction information (sender, receiver, amount)
-   Terms:
    -   Confidential if amount is hidden, but sender and receiver are public
    -   Unlinkable if transaction appears to be independent of others, its inputs/outputs also appear independent of each other
    -   Untraceable if the lineage of coins/assets are unattainable
    -   Anonymous if sender, receiver and amount are all hidden
-   Solutions:
    -   Obfuscation $\approx$ k-anonymity set (hiding in the crowd)
        -   Pros
            -   Simpler to construct
            -   More efficient
        -   Cons
            -   Only unlinking senders/receivers
            -   Privacy depends on anonymity set size and set sampling
        -   Examples
            -   CoinJoin
            -   Mixing services
    -   Cryptography $\approx$ using fancy crypto magic to hide information yet remains varifiable
        -   Pros
            -   Maximal provacy
            -   Selective attributes hiding
        -   Cons
            -   Computationally heavy
            -   Complicated to implement
        -   Examples
            -   Zerocash
            -   Zerocoin
            -   Ring singnatures
            -   CoinShuffle



### CoinJoin and CoinShuffle

#### CoinJoin

-   Equal-output v.s. Variable-output
    -   Variable-output
        -   Still leaking sender-receiver relation
    -   Equal-output
        -   Pros
            -   True Unlinkability
            -   Leads to equal-input CoinJoin in the next generation
        -   Cons
            -   Much more UTXO
            -   Painful for arbitrary or small change value
-   Equal-output & equal-input > equal-output = equal-input > variable-output & variable-input
-   Equal-output/input constraint is universal to obfuscation-based solutions

#### CoinShuffle

-   Decentralized Mixing + CoinJoin
-   Anonymous Group Messaging
-   Idea: everyone choose a random permutation that contributes to the shuffle
-   CoinShuffle++ is more efficient and scale well with anonymity size



### Zerocash [BCG+14]: A Primer

-   Idea: using zero-knowledge proof (zk-SNARK) to prove the validity of a transaction with hidden sender, receiver, and amount values
-   Basic intuition: have one cryptographic proof $\pi$ that proves:
    -   sender, receiver, and amount encryptions
    -   amount has not been double-spent
-   zk-SNARK
    -   Zero-Knowledge
    -   Succinct (short size, short verification time)
    -   Non-interactive
    -   Argument
    -   of Knowledge

#### Attempt 1

-   Mint with serial number $s_n$
-   Spend with serial number $s_n$
-   Pros:
    -   Cannot double spend

#### Attempt 2

-   Mint with commitment $c_m$
    -   $c_m = COMM(s_n, r)$
-   Spend with $s_n$, $r$
-   Pros:
    -   Others cannot spend my coins (only I have r)

#### Attempt 3

-   Mint
-   Spend with $s_n$, $\pi$
    -   $\exist: c_m \in$ list of prior commitments
    -   Well-formed: $c_m = COMM(s_n, r)$
-   Pros:
    -   Untraceable spend/payment

#### Attempt 4

-   Variable denomination
-   Mint coin of value $v$
    -   $c_m = COMM(v, COMM(s_n, s), r)$
-   Spend with $s_n$, $v$, $\pi$
-   Pros:
    -   Variable denomination

#### Attempt 5

-   Introducing payment address
-   Mint
    -   $c_m = COMM(v, COMM(pk, t, s), r)$
-   Spend
    -   $s_n = PRF(sk, t)$

#### Attempt 6

-   Direct payment
-   Mint
-   Spend with $s_n$, $c_m$, $\pi$
    -   $c_m^B$ calculate the commitment that B can unlock
-   Pros:
    -   Hides sender, receiver, amount





## Scaling Blockchain

### Problems & Challenges

**TBD**

