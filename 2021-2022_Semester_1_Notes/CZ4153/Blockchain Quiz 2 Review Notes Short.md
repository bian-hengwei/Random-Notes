# Blockchain Quiz 2 Review Notes

[toc]

## Blockchain Security

### Attacking Blockchains

#### Blockchain architecture:

-   Smart Contract (Re-entry, Overflow, Race condition)
-   Runtime (EVM) & Consensus (51%, Collusion, Censorship, Selfish mining, Front running...)
-   P2P Network (BGP hijacking, Eclipse attack)

#### Characteristics of (Permissionless) Blockchain Security

-   Open access
-   Immutability
-   Cryptoeconomics
-   Less time tested & constantly changing



### Consensus Security

-   51% Forking
-   Selfish Mining
    -   **Brings down the security threshold down from 51% to**
        -   25% if you can reach 50% of the network faster
        -   33% if you lose propogation races
-   Stubborn Mining
    -   Generalizes selfish mining strategy
-   Defense: Selfish Mining & Block Withholding Attacks
    -   Idea: increase cost of block withholding and reward for publishing early
-   Trailer: GHOST
    -   Greedy Heaviest-Observerd SubTree
    -   Deal with blockchain scaling problems

#### Frontrunning Attack

-   Idea: frontrun & reordering
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

-   Surround a part of the blockchain by attackers
-   Partition the blockchain
-   Double spending may be performed



### Smart Contract Security

-   Overflow & underflow bug
-   Reentrancy bug
-   MultiSig hack bug





## (Pseudo) Anonymity and Linkability

### Linkability in Bitcoin

-   Bitcoin Transaction Graph
    -   Bitcoin blockchain may be viewed as an evolving acyclic transaction graph
-   Bitcoin Address Graph
    -   Transaction graphs leads to an evolving address graph that may have cycles
-   Clustering Addresses to Users
    -   Clustering heuristics may be derived from idiom of use (multiple inputs to a transaction is likely to originate from the same user)
-   Bitcoin User Graph
-   Linking Users to Users
    -   Clustering heuristics may be derived from idioms of use (output is most likely change address if it is new and is not used regularly)
-   Bitcoin Deanonymization
    -   Bitcoin is susceptible to “deanonymization” analysis due to address linkability



### Unlinkable Transactions

#### Peer-to-Peer Mixing: CoinJoin

-   Idea: instead of building a transaction alone, build one together with other peers
-   Provides unlinkability using standard multi-signature transactions
-   All owners must sign
    -   No loss of coins
-   Main problem: the builder knows the entire mapping

#### Mixing-as-a-Service

-   BitLaundry
    -   Does not guarantee full unlinkability
    -   Threats:
        -   Mixing service may just steal the money
        -   Mixing service may log or reveal the information on address mapping
    -   CoinJoin  & Mixing
        -   Coinjoin reduces the trust on mixing service and distributes it in a peer-group
        -   It reduces the anonymity level as well; makes it dependent on peer-group size





## Blockchain Privacy

### Overview of Privacy Solutions

-   Challenge: maintaining public verifiability while shielding transaction information (sender, receiver, amount)
-   Solutions:
    -   Obfuscation $\approx$ k-anonymity set (hiding in the crowd)
        -   Pros
            -   Simpler to construct
            -   More efficient
        -   Cons
            -   Only unlinking senders/receivers
            -   Privacy depends on anonymity set size and set sampling
    -   Cryptography $\approx$ using fancy crypto magic to hide information yet remains varifiable



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
-   Data availability: all states are globally aggregated at beacon chain level
-   State validity: PBFT at shard chain level ensures correctness of each shard block, even if a shard is  corrupted, users could challenge the (malicious) shard validators to provide proof of state transition at the beacon chain

#### Sharding (commit block header)

-   Beacon chain
-   Shard chain
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



### Scaling Computation - Layer 2

-   Problem: every node processes all tx, stores all data, requires total ordering of all tx
-   Idea: push tx and state updates off-chain and only broadcast computation results on-chain



### Scaling Computations - Channels

#### Generic Channel Workflow

-   Setup: a fixed group intialize states
-   Offchain progression: all parties cooperatively agree/sign on the new state
-   Onchain dispute (challenge-response): honest party can always finalize/close channel with latest signed states, or force progression onchain with an unresponsive counterparty
-   Dispute resolution: fast-forward to the latest state presented as dispute evidence

#### Payment Channel (mostly for Bitcoin)

-   Replaced by incentive (spilman channel)

-   Replaced by time lock (Duplex Micropayment Channel/DMC)

-   Replaced by revocation (lightning network)

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
-   Pros:
    -   State validity ensured by fraus proof => economically bounded security
    -   Support EVM compatible smart contract
    -   Finality only needs 1 block confirmation
-   Cons:
    -   Always online assumption OR 3rd party
    -   Withdrawal takes 1 week
