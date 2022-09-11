Intro

This is a document comparing Polkadot's PoV mechanism with Optimistic rollup technology as follow up of the email from the 7th Sept 2022. 

1. What is Polkadot's Pov
2. What is Optimistic rollup
	1. optimism
	2. arbitrum 
	3. zk
3. Architecture compare and contrast
	1. Polkadot area of activity regarding PoV covers validation/commitment/finality and availability 
		1. For the validation flow, following behaviour is specified
			1. Assumption: Parachain provided STVF on the Relay Chain 
			2. Parachain process called collator is a parachain full node and builds a PoV block
			3. Randomly assigned validators in relay chain obtain parachain's  PoV block and validate it using STVF. VRFs guide validators' assignment.
			4. If OK then the parachain block header is signed. When there is enough signatures the header can be placed on Relay Chain
			5. Validators may broadcast back the parachain's block to the parachain to cover the case of censored collators 
			6. Full nodes in the parachain can dispute transitions in the block and affect BABE and GRANDPA behaviour. If malicious activity is proved then the block is not finalized and corrupted validators are slashed
			7. To summarize: PoV - Proof of Validity - is information used in the protocol of the Relay Chain. In initial stages of the protocol it is evaluated using runtime committed to the Relay Chain and specific for the originating parachain. Later on it is treated as a blob. Parachains track it and use it to share security of the Relay Chain. 
		2. For the availability Polkadot balances scalability with data availability by creating erasure codes. The codes are broadcasted to other validators. They are used to reconstruct the PoV and pre-vote/vote on the finally by GRANDPA 
	2. Rollups 
		1. There are two distinct layers of rollup architecture: on-chain contract and off-chain execution systems of the 3rd party
		2. Rollups are primarily built for scalability. It is achieved by offloading computation to the off-chain system. The on-chain part is for inheriting on-chain security guarantees in the off-chain world
		3. On-chain rollup contract is specifically written for the 3rd party use-case
		4. Every transition summary is re-run in the on-chain contract, so it's publicly available
		5. It can be contested using Proof of Fraud. In reality, to speed up withdrawals from the rollup contract larger entities re-run changes on their own, to be able to tell if the rollup is in the correct state. They don't need to wait for the PoF to allow a withdrawal then.
	3. Comparison of PoV and Rollups
		1. In general, Rollups are equivalent to whole Parachains
		2. Both Parachains and Rollups are 3rd party systems with arbitrary guaranties. Important difference is that for parachains, we have to-go framework: Substrate, where as rollups are not standardized 
		3. PoV is part of the protocol of the Relay Chain that was designed to share global security with 3rd party parachains in a decentralized way. It is used as meta information to validate parachain state in a stateless way. It is also used to provide fraud proofs
		4. There is no direct equivalent of PoV in Rollups as PoV is a more generic protocol concept. It reaches deeper to the custom parachain to eventually provide finality of the consensus, avoid censorship and facilitate composing of a larger ecosystem of parachains
		5. Rollups communicate with on-chain contracts with custom transactions that provide similar to PoV transitions. Yet, the reason to have the on-chain commitments is rather to prevent fraud. 
		6. The advantage of Rollup is larger liquidity as on-chain rollup contract is almost equivalent to DEX
		7. The advantage of Parachain with PoV communication is build-in communication with another parachains and an attempt to standardize finality, which in turn motivate to extend ecosystem of parachains
		8. Disadvantage of Rollups is much lower openness about inner workings of the protocol 
		9. Disadvantage of Parachain is complexity of the protocol as it has to be more generic (e.g. cases like: validators are randomized but still can collude; forks may happen in Relay Chain, cause more protocol paths to be considered)
		10. Interchain (cross parachain or between rollups) messaging is simpler with Polkadot as message hashes are secured in metadata along PoV and require yet another 3rd party to implement custom protocol.
