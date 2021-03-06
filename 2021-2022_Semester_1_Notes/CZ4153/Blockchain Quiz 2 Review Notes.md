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
    -   The User Graph may be further ???clustered??? to obtain linked users (entities).
    -   Clustering heuristics may be derived from idioms of use (output is most likely change address if it is new and is not used regularly)
-   Bitcoin Deanonymization
    -   Bitcoin is susceptible to ???deanonymization??? analysis due to address linkability



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

-   Throughput of blockchain is relatively low
    -   10tps for ethereum
    -   7tps for bitcoin
-   "Scalability"
-   Scalability & Throughput
    -   Higher throughput => more tx per block & shorter mining intervals
    -   Hardware requirement increases => only powerful server can participate => lower decentralization
    -   Shorter intervals => more forks => less security
-   Scalability & Adding resources
    -   Vertical scaling: adding resources to a single node => centralization problem again
    -   Horizontal scaling: adding new nodes to  the system => slower consensus, higher overhead, worse performance

#### Fundamental Problems in Blockchain Scalability

-   Replicated computation
-   Replicated storage
-   Consensus overhead
-   Solution: process partial tx, store partial data, have more efficient consensus
    -   **State validity**
    -   **Data availability**
    -   Byzantine adversary



### Scaling Computation - Sharding

-   Idea: horizontally scale by partitioning transaction processing into shards, and parallelizing their execution
-   Challenge:
    -   Adversarial network
    -   Byzantine-failure with sybil identity

#### Strawman Sharding

-   Each shard has their own validators and manage their chain seperately
-   Problems:
    -   Reduces security without random validator assignment
    -   Cannot send cross-shard transaction
-   Solution: random validator assignment
    -   Pros:
        -   Don't reduce security much
    -   Cons:
        -   Still can be compromised
-   Solution: randomly shuffle/rotate validators occasionally
    -   Need a random beacon chain

#### Sharding Basics

-   Unbiased randomness
    -   RandHound/RandHerd
    -   Verifiable Random Function (VRF)
-   What to commit
    -   Entire block: transaction sharding only
        -   Global state stored in beacon chain
        -   Cross-shard transactions are synchronous
    -   Block header: transaction & state sharding
        -   Data are also sharded
        -   No global state
        -   Cross-shard transactions are asynchronous
-   Asynchronous cross-shard transaction
    -   Atomicity: all-or-nothing (train-hotel problem)

#### Sharding (commit entire block)

-   Beacon chain
    -   Provide randomness
    -   Facilitate cross-shard tx
    -   Aggregate cross-link into finalized block
    -   Validate signatures on the shard blocks + process cross shard transactions + aggregate/merkelize all raw blocks into a global state tree
    -   Requires more hardware resources (storage), but does not require a lot of computational power
-   Shard chain
    -   Run efficient BFT consensus
    -   At most a few thousands validators in BFT
    -   Random validator shuffling method
-   Steps:
    1.   Identity establishment using PoW
    2.   Assigning shard commitee using RANDAO
    3.   Intra-commitee consensus using PBFT
    4.   Final consensus broadcast through committing entire block
    5.   Generate next epoch randomness
-   Data availability: all states are globally aggregated at beacon chain level
-   State validity: PBFT at shard chain level ensures correctness of each shard block, even if a shard is  corrupted, users could challenge the (malicious) shard validators to provide proof of state transition at the beacon chain

#### Sharding (commit block header)

-   Beacon chain
    -   Almost the same
-   Shard chain
    -   Run efficient BFT consensus
-   Threshold PoS + TxFlow
-   VRF based on stake
-   Quadratic sharding
-   Cannot add infinite amount of shards due to the limit of beacon chain
-   State validity:
    -   Fisherman proof (Fraud proof)
    -   Non-interactive cryptographic proof (zk-SNARK)

#### Fisherman proof

- Idea:
  - Before a predefined challenge period elapsed, anyone can question the computation integrity at certain locality
  - One that computes the result must provide an evidence in time, otherwise failure to respond is used as a proof of fraud

#### ETH and BTC

-   Etheurum sharding: Main Chain, Beacon Chain, Shard Chain, VM (inspectors)

-   Clockchain interoperability $\approx$ state sharding

    -   COSMOS

    -   Polkadot

    -   | State Sharding                     | Cosmos                                                       | Polkadot            |
        | ---------------------------------- | ------------------------------------------------------------ | ------------------- |
        | Beacon chain                       | Cosmos Hub                                                   | Relay Chain         |
        | Shard chain                        | Cosmos Zone                                                  | Para Chain          |
        | All shards run BFT or PoS variants | All use Tendermint Consensus OR use any consensus then bridge to Tendermint | Pluggable consensus |
        | Cross-shard txn                    | Inter blockchain Communication (IBC)                         | Cross-chain txn     |



### Scaling Computation - Layer 2

-   Problem: every node processes all tx, stores all data, requires total ordering of all tx
-   Idea: push tx and state updates off-chain and only broadcast computation results on-chain
-   Layered scaling
    -   Layer 1:
        -   Sharding
        -   Compact tx/state encoding
        -   Faster client implementation
        -   Efficient "supreme court"
    -   Layer 2:
        -   Channel-based approach
        -   Commit-based approach
        -   "local court"
-   Benefits of having layer 2
    -   2 tx to interact with main chain
    -   Drastically reducing replicated computation
    -   Lower gas price
-   Layer 2 solutions:
    -   Channel-based
        -   Payment channel
            -   Spilman channel
            -   DMC
            -   Lightning network
        -   State channel
    -   Commit-chains
        -   Plasma
            -   Minimal Viable Plasma
            -   Plasma Cash
        -   Rollup
            -   Optismistic
            -   zk



### Scaling Computations - Channels

#### Generic Channel Workflow

-   Setup: a fixed group intialize states
-   Offchain progression: all parties cooperatively agree/sign on the new state
-   Onchain dispute (challenge-response): honest party can always finalize/close channel with latest signed states, or force progression onchain with an unresponsive counterparty
-   Dispute resolution: fast-forward to the latest state presented as dispute evidence

#### Payment Channel (mostly for Bitcoin)

-   Replaced by incentive (spilman channel)

    -   One-way/unidirectional payment

-   Replaced by time lock (Duplex Micropayment Channel/DMC)

    -   Bi-directional payment with expiry time

-   Replaced by revocation (lightning network)

    -   Bi-directional & no expiry
    -   Using Hash Time Lock Contract (HTLC) to connect channels into a network

-   Pros:

    -   Instant finality
    -   Low cost
    -   Private (only known within the channel)
    -   Best suited with a fixed group with high tx volumn

-   Cons:

    -   High collateral lookups for routing hub => capital inefficiency, potential point of failure
    -   "Always online assumption"
    -   User responsible for data availbility
    -   Multi-hop/virtual channels are slightly complex during dispute resolution

#### Commit Chain

-   Complexity of routing will drive channel network into a "Hub-spoke" topology
-   Sidechain uses trusted federation to facilitate a 2-way peg into/outof another blockchain

#### Generic Commit-chain Workflow

-   Step 1: deposit
-   Step 2: tx in commit-chain
-   Step 3: withdraw
-   Involves periodical commit scheme
-   Fraud proof submitted to main chain if fraud is found
-   What to commit:
    -   State root only: plasma
    -   State root + all tx: rollup
-   Commit-chain v.s. sharding
    -   Similar architecture & approaches
    -   Commit-chain:
        -   Accept local faults but try to limit their consequences
        -   More decoupled from the validity of layer 1
    -   Sharding:
        -   Try to ensure total validity/availability of every part of the system
        -   Requires "tight coupling"



### Scaling Computation - Rollup

#### ZK-Rollup

-   Idea:
    -   Use zk-SNARK to provide validity proof of new state root
    -   Use layer 1 for data availability by posting all tx data on-chain
-   Steps (transfer):
    -   tx msg + sig
    -   apply tx and calculate new state root
    -   new rollup block
    -   generate validity proof + commit
-   Steps (deposit):
    -   deposit to rollup contract
    -   append tx to inbox
    -   calculate new state root
    -   new rollup block
    -   generate validity proof + commit
-   Steps (withdraw):
    -   withdraw from rollup contract by providing a merkle proof of balance in the state tree
    -   verify the merkle proof and immediately refund
    -   append tx to inbox
    -   calculate new state root
    -   new rollup block
    -   generate validity proof + commit
-   Pros:
    -   State validity ensured by validty proof, prevents corruption and stealing
    -   Data availability bypassed by posting all data on-chain
    -   Safety does not require "always online assumption"
    -   Finality is around 10 minutes - proof generation time
-   Cons
    -   General computation expensive
    -   Not compatible to EVM
    -   Throughtput bounded by 300 tps
    -   Hard to implement

#### Fraud Proof + Optimistic Rollup

-   Idea: Relax validity proof to fraud proof to allow optimistic execution of general computation
-   ORU - State Transition
    -   tx msg + sig
    -   apply tx and calculate new root
    -   new rollup block
    -   commit
-   ORU - Dispute assertion
    -   challenge a block
    -   verify challenge & reward challenger & slash validator
-   Pros:
    -   State validity ensured by fraus proof => economically bounded security
    -   Support EVM compatible smart contract
    -   Finality only needs 1 block confirmation
-   Cons:
    -   Always online assumption OR 3rd party
    -   Withdrawal takes 1 week

#### Compare

|                | Validity Proof | Fraud Proof       |
| -------------- | -------------- | ----------------- |
| Data On-chain  | ZK Rollup      | Optimistic Rollup |
| Data Off-chain | ZK Plasma      | Plasma            |

