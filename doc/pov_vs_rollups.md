### Table of Contents

1. Intro
	1. Quick summary of Polkadot and PoV
	2. Quick summary of Rollup options
3. PoV related protocol part
4. Rollup stack
5. Comparison

### Intro

#### What is Polkadot and PoV?

Polkadot is a protocol to facilitate growing ecosystem of heterogeneous cooprating chains. It is build out from Relay Chain as top level meta layer and Parachains as bottom layer shards with arbitrary logic and rules. 

PoV, Proof of Validity, is a construct emitted by special Parachains nodes, which implements the interoperability between Relay Chain and a Parachain. The protocol of handling PoV benefits Parachains the crypto economic security of the whole Relay Chain. 

#### What are Rollups?

Rollups are scalability solution devised for Ethereum network. They are however quite generic concept. It uses top level chain to host a contract, which tracks asset transitions in a rollup. Rollup itself exists in 3rd party network and runtime, with arbitrary logic, API and protocol. It is primarly build for performance and cheap operations. 

There are two main types of rollups: optimistic and ZK. The difference is in validation mechanism. For Optimistic Rollups it is post-commit Proof of Fraud. For ZK it's pre-commit validation with ZK proof showing state transition. 

#### PoV protocol 

Polkadot area of activity regarding PoV covers validation/commitment/finality and availability 

##### Validation
For the validation flow, following behaviour is specified
1. Assumption: Parachain provided STVF on the Relay Chain. This is how Relay Chain can execute and validate state transition provided in PoV.
2. Parachain process called Collator is a parachain full node and builds a PoV block
3. Randomly assigned validators in relay chain obtain parachain's  PoV block and validate it using STVF. VRFs guide validators' assignment.
4. If OK then the parachain block header is signed. When there is enough signatures the header can be placed on Relay Chain
5. Validators may broadcast back the parachain's block to the parachain to cover the case of censored collators 
6. Full nodes in the parachain can dispute transitions in the block and affect BABE and GRANDPA behaviour. If malicious activity is proved then the block is not finalized and corrupted validators are slashed
7. To summarize: PoV - Proof of Validity - is a datastructure used in the protocol between the Relay Chain and a Parachain. In initial stages of the protocol it is evaluated using runtime committed to the Relay Chain and specific for the originating parachain. Later on it is treated as a blob. Parachains track it and use it to share security of the Relay Chain and to interpret finallity of its blocks

##### Availability	

For the availability Polkadot balances scalability with data availability by creating erasure codes. The codes are broadcasted to other validators. They are used to reconstruct the PoV and pre-vote/vote on the finally by GRANDPA 


Above sections explain Polkadot's teminology: Collation > Availability > Approval Checking > Disputes

#### Rollup stack

1. There are two distinct layers of rollup architecture: on-chain contract and off-chain execution systems of the 3rd party
2. Rollups are primarily built for scalability. It is achieved by offloading computation to the off-chain system. The on-chain part is for inheriting on-chain security guarantees in the off-chain world
3. On-chain rollup contract is specifically written for the 3rd party use-case
4. Every transition summary is re-run in the on-chain contract, so it's publicly available
5. It can be contested using Proof of Fraud. In reality, to speed up withdrawals from the rollup contract larger entities re-run changes on their own, to be able to tell if the rollup is in the correct state. They don't need to wait for the PoF to allow a withdrawal then.

#### Compare and Contrast
Comparison of PoV and Rollups

1. In general, Rollups are equivalent to whole Parachains
2. Both Parachains and Rollups are 3rd party systems with arbitrary guaranties. Important difference is that for parachains, we have to-go framework: Substrate, where as rollups are not standardized 
3. PoV is part of the protocol of the Relay Chain that was designed to share global security with 3rd party parachains in a decentralized way. It is used as meta information to validate parachain state in a stateless way. It is also used to provide fraud proofs
4. There is no direct equivalent of PoV in Rollups as PoV is a more generic protocol concept. It reaches deeper to the custom parachain to eventually provide finality of the consensus, avoid censorship and facilitate composing of a larger ecosystem of parachains
5. Rollups communicate with on-chain contracts with custom transactions that provide similar to PoV transitions. Yet, the reason to have the on-chain commitments is rather to prevent fraud. 

| Polkadot |  | Rollups | | 
|---|---|---|---|
| Good | Bad | Good | Bad |
| PoV based protocol: build-in communication <br/> with another parachains and an attempt<br/> to standardize finality, which in turn motivate<br/> to extend ecosystem of parachains | |Can use top level assets for communication with outside world | Requires cooperation of two closed protocols to communicate. |
|||The advantage of Rollup is larger<br/> liquidity as on-chain rollup contract<br/> is almost equivalent to DEX and re-uses<br/> top level currency (eth) as collateral|
||||Disadvantage of Rollups is<br> much lower openness about<br> inner workings of a protocol |
||Disadvantage of Parachain is<br> complexity of the protocol as it<br> has to be more generic<br> (e.g. cases like: validators are randomized<br> but still can collude; forks may happen<br> in Relay Chain. It causes more protocol<br> paths to be considered)||
|Interchain (cross parachain or between rollups)<br/> messaging is simpler with Polkadot<br/> as message hashes are secured in metadata<br/> along PoV and require yet another<br/> 3rd party to implement custom protocol|||
|||Advantage of Rollup is narrow<br/> usecase - scalability solution.<br/> Eventually it leads to fast <br/>and cheap operations on the 3rd party rollup.<br/> 3rd party is responsible only for single subsystem.<br/> Top level chain doesn't interfere with it and is maintained by toplevel protocol team (Ethereum)||
|Having Parachain's runtime on Relay Chain is quite powerful idea. Especially it's WASM upgradable code. There is no dependency on top level chain (Ethereum) VM|||Rollups depend on capabilities of the top level chain |
|||Rollup team may focus only on local buisness logic. It may improve productivity.||

### Summary 

The main difference between PoV and Rollups protocols is that the first one focuses on deeper open protocol to eventually grow multichain ecosystem and the later one on scalability and performance. 



#### Standardization of concepts

| Generic term | Polkadot | Rollups | 
|---|---|---|
| Layer 1 | Relay Chain | e.g. Ethereum  |
| Layer 2 (or local Layer 1s) | Parachain  | 3rd party Rollup  |
| Fraud prevention  | Disputes/Fisherman | Proof of Fraud for OR |
| Correctness | Backing | ZK proof |
| Finallity | Relay Chain | Ethereum | 
| Block production | Collators | Private system |
