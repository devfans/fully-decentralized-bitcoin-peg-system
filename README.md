---
description: >-
  How much decentralization can a Bitcoin 2-way peg system achieve? A fully
  decentralized 2-way peg system.
---

# fully-decentralized-bitcoin-peg-system

Scaling Bitcoin chain with side chains or layer 2 chains is thought as one of the potential solutions to gain more transaction throughout and extend the functionality. To build such a chain, a valid 2 way peg system is required to ensure Bitcoin is freely and easily pegged from the base chain to the side chains or layer 2 chains and pegged back when needed, we call the latter as layer 2 chains in the following context. So we commonly think about how to design such a peg system. I did some research on how sBTC by Trustmachine works, and the design of PowPeg by RSK project, and analyze the tradeoffs they made to bring decentralization and safety to a 2-way peg system, and then I am proposing a potential new way to gain more decentralization on a peg system, a fully decentralized Bitcoin Peg.

Before that, we need to have a clear definition for such a valid 2-way peg system. Here I am describing the validation with three factors with some explaination.

* Full Decentralization. Decentralization is the main characteristic brought by Satoshi Nakamoto with the Bitcoin design. Centralized parties, authorities, federations bring potential risks to a system, since the system depends on the safety assumption of these parties and there is not a novel way to make a party fully trust-worthy. Here we say full decentralization, because there're some extra properties a system should have.
  * Transparent. A decentralized system might be operated by a group of members. The way of how such a group operate the system and how the group members are selected should be transparent. The operation target might be privacy protected, but the mechanism should be transparent. So any party can verify against it and then make a decision to endorse or disagree.
  * Open Membership. Anyone or any party should be able to join or leave such a system freely when it's a valid operation. It should be permissionless. Say a system is operated by a group of signers selected in a decentralized way, however, a new signer want to join such a system with requiring the permission of the current signer group. It's hence not a fully decentralized system. Open membership protects any party's right to such a system. it's important to avoid censorship.
* Safety. Safety is the ensurance on the value owned by system users. Imagine a peg system is operated by a group of signers with a multi-sig wallet, and the group of signers have no bonds or limitation by the system protocol. Then they are free to maliciously operate the locked Bitcoin on the base chain. The peg token on the layer 2 chains will be valueless and peg users have to take the loss due to the lack of safety.
* Liveness. A peg system should be usable with enough availability. If a system is designed as fully decentralized and safety enough, and, however, the cost to operate system is very high. This might makes the system unable to run due to lack of operators. Thus, the liveness is too low. The efficiency of the monetary liquidity should be taken to consideration when design such a system. The system is desired to stay operable even in extreme odd situations.

Why building such a valid 2 way peg system is hard on Bitcoin? The Bitcoin chain is thought as the most decentralized, secure and duration blockchain. However, another property of the chain is the simplicity, it lacks the full expressive smart contracts even nowadays most of the newly created chain supports such a feature. This does not indicate it's bad though since functionality extension might come with extra security risk. However, this truly limited the implementation of a decentralized 2 way peg system. In the contrast, decentralized 2 way peg system are not hard to design on the Ethereum based layer 2 chains.

sBTC designed by the trustmichines combines the consensus of its layer 2 chain with its peg system by a stackers role. Layer 2 chain miners need to bids on the block producer role by transferring BTC to stackers. Stackers are a group of signers who stake their Layer 2 token (STX) to operate the peg system, and the value of the locked layer 2 tokens should be higher than they can potentially steal from the peg wallet. In this design, the threshold level of the peg wallet and locked layer 2 tokens provides the security of the systems(Penalty with the locked layer 2 token is not mentioned in the white-paper indeed), and the economic incentive compatibility provides the liveness. Some good properties it has indeed.

* Anyone can elect to be a stacker by following the protocol, staking their layer 2 tokens.
* Compromising the peg wallet needs a threshold level. Normally it's hard to achieve this if it's designed as not incentivized.
* Economic incentives gives the peg system operator rational to behave honestly.
* The pegging requests are broadcasted on the base chain, to gain the censorship resistance from the Bitcoin chain.
* Recovery mode to keep liveness even the threshold level is not met.

However, when think carefully, there might be some flaws as below.

* Open membership might is not that open as said. The shift the signers will require the consensus of the current signer group. If group A gains much benefits to be convinced to deny on the change to the share of them, membership shift might be hard. So membership is not fully permissionless.
* How to treat the locked layer 2 tokens owned by the stackers? The white paper does not mention the slash or how to slash. At the worst case which has no slash on stackers, the group will be incentivized to collude in stealing the Bitcoin in peg wallets. If there's slash with these locked layer 2 tokens, how to process that will need some further design consideration. The performance of each signer needs to be estimated and the liveness issue of group part should not impact the rewards on honest ones because it does not make much sense.
* The path of degraded of threshold level in the peg wallet degrades the security too. Normally security should not be the cost of the liveness.
* The recovery mode may wont work out. If reward value generated by the PoX protocol is much less than the values in the peg requests, the liveness is pretty low indeed.
* The on-chain price oracle. The page mentions the oracle is fully on chain and decentralized, but without details for trust-worthy.

PowPeg by the RSK project is adopting HSMs to keep private keys away from the peg notaries. So, even the majority of signers wont have the ability to maliciously release the locked Bitcoins. According to the design, peg notaries need to run the PowHSM devices which are connected to RSK network(a Bitcoin merge-mining layer 2 chain), and the PowHSM only proceeds to sign a peg-out transaction upon receiving commands from the RSK blockchain, backed by 4000 confirmation blocks, with a cumulative proof-of-work currently equivalent to approximately 100 bitcoin blocks. In addition, if there's a censorship occurs by the peg functionaries, then the functionaries cannot continue to perform other peg-outs, as peg-outs are linked with UTXOs, and functionaries cannot choose the UTXOs for the peg-out transactions. The peg-out UTXOs, including “change” UTXOs, are selected by the Bridge contract, forming a consensus-enforced chain. With the trust assumption on these HSMs, and even extra attestation against the firmware running in them, the peg system becomes less risky as the old federated system solution. So good properties it argues includes:

* Peg notaries can not maliciously operate the locked Bitcoin, even with a majority.
* Proof of work verification in HSMs brings more security against malicious peg out requests.
* Censorship resistance with linked UTXOs.

Though it's still facing some factors of flaws or potential weakness.

* Trust-worthy HSM may not be transparent as they think. HSM involves not just the firmware, also the physical hardware. This brings extra security assumptions.
* These HSMs are hosted by the peg notaries and connected to a RSK node. How is the firmware update managed and the HSM's trust assumption on the RSK node needs to be audited and verified. Without knowing the details, there might be some potential risks.
* The peg notary group shift needs to go through a vote phase, that means the group composition change needs the permission of the current peg notaries, so not fully permissionless.

It's hard for the 2 way peg system to achieve full decentralization and safe enough without losing liveness. Extra safe assumption should be avoided when possible. However the commonly ideas are clear somehow. Like the stake or collateral to provide the safety in a system. It works well in the Ethereum PoS consensus and MakerDao's DAI system. The idea is that it's hard to ensure every part to behave 100% honestly, but we can ensure it malicious behave at enough cost to keep the entire system still live well. Here, I propose a potential solution of a novel 2 way Bitcoin Peg system between the base chain and a layer 2 chain.



<figure><img src="https://lh5.googleusercontent.com/lg0zITunH2I9SV1VEcIFHNWe1lqGzuqcwK338lm0Z8e4Sch67jwaCjlddKBj_XeB1OLdoJsgOcvFegT1XE830BvOiSNiQFYnYx6wI5n0mQDUNIq1s7vdfB2CloOR3M8rSjxdgxvTQRxz0Ni-DiiBnwU" alt=""><figcaption><p>Decentralized Peg System Design</p></figcaption></figure>

*



The designer of concept drivechains submitted a BIP to introduce a trust-minimized peg system for side chains. However it requires changes on the base chain, which looks stilll far from materialization. Here it's not in the discussion.
