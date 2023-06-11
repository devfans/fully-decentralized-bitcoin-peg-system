---
description: >-
  How much decentralization can a Bitcoin 2-way peg system achieve? A fully
  decentralized 2-way peg system.
---

# fully-decentralized-bitcoin-peg-system

Scaling Bitcoin chain with side chains or layer 2 chains is thought as one of the potential solutions to gain more transaction throughout and extend the functionality. To build such a chain, a valid 2-way peg system is required to ensure that Bitcoin is freely and easily pegged from the base chain to the side chains or layer 2 chains and pegged back when needed, we call the latter as layer 2 chains in the following context. So we commonly think about how to design such a peg system. I did some research on how sBTC by TrustMachine works, and the design of PowPeg by RSK project, and analyze the tradeoffs they made to bring decentralization and safety to a 2-way peg system, and then I am proposing a potential new way to gain more decentralization on a peg system, a fully decentralized Bitcoin Peg.

Before that, we need to have a clear definition for such a valid 2-way peg system. Here I am describing the validation with three factors with some explanation.

* Full Decentralization. Decentralization is the main characteristic brought by Satoshi Nakamoto with the Bitcoin design. Centralized parties, authorities, federations bring potential risks to a system, since the system depends on the safety assumption on these parties and there is not a novel way to make a party fully trust-worthy. Here we say full decentralization, because there're some extra properties a system should have.
  * Transparent. A decentralized system might be operated by a group of members. The way of how such a group operate the system and how the group members are selected should be transparent. The operation target might be privacy protected, but the mechanism should be transparent. So any party can verify against it and then make a decision to endorse or disagree.
  * Open Membership. Anyone or any party should be able to join or leave such a system freely when it's a valid operation. It should be permissionless. Say a system is operated by a group of signers selected in a decentralized way, however, a new signer joins such a system with requiring the permission of the current signer group. It's hence not a fully decentralized system. Open membership protects any party's right of access to such a system. it's important to avoid censorship.
* Safety. Safety is the insurance on the value owned by system users. Imagine a peg system is operated by a group of signers with a multi-sig wallet, and the group of signers have no bonds or limitations from the system protocol. Then they are free to maliciously operate the locked Bitcoin on the base chain. The peg token on the layer 2 chains will be valueless and peg users have to take the loss due to the lack of the safety.
* Liveness. A peg system should be usable with enough availability. If a system is designed as fully decentralized and safety enough, but, however, the cost to operate such a system is very high. This might makes the system unable to run due to lack of operators. Thus, the liveness is too low. The efficiency of the monetary liquidity should be taken into the consideration when designing such a system. The system is desired to stay operable even in extreme odd situations.

Why building such a valid 2 way peg system is hard on Bitcoin? The Bitcoin chain is thought as the most decentralized, secure and duration blockchain. However, another property of the chain is the simplicity, it lacks the full expressive smart contracts, even nowadays most of the newly created chain supports such a feature. This does not indicate it's bad though since functionality extension might come with extra security risk. However, this truly limited the implementation of a decentralized 2-way peg system. In the contrast, a decentralized 2-way peg system is not hard to design for the Ethereum based layer 2 chains.

sBTC designed by the TrustMachines combines the consensus of its layer 2 chain with its peg system by a stacker role. Layer 2 chain miners need to bids on the block producer role by transferring BTC to the stackers. Stackers are a group of signers who stake their layer 2 token (STX) to operate the peg system, and the value of the locked layer 2 tokens should be higher than they can potentially steal from the peg wallet. In this design, the threshold level of the peg wallet and the value of locked layer 2 tokens provides the safety of the systems(Penalty with the locked layer 2 token is not mentioned in the white-paper indeed), and the economic incentive compatibility provides the liveness. Some good properties it has indeed.

* Anyone can elect to be a stacker by following the protocol, staking their layer 2 tokens.
* Compromising the peg wallet needs a threshold level. Normally it's hard to achieve this if it's designed as not incentivized.
* Economic incentive gives the peg system operator rationale to behave honestly.
* The pegging requests are broadcasted on the base chain, to gain the censorship resistance from the Bitcoin chain.
* Recovery mode to keep the liveness when the threshold level is not met.

However, when think carefully, there might be some flaws as below.

* Open membership might be not that open as said. The shift the signers will require the consensus of the current signer group. If group A gains much benefits to be convinced to deny on the change to the share of them, membership shift might be hard. So membership is not fully permissionless.
* How to treat the locked layer 2 tokens owned by the stackers? The white paper does not mention the slash or how to slash. At the worst case which has no slash on stackers, the group will be incentivized to collude in stealing the Bitcoin in peg wallets. If there's slash with these locked layer 2 tokens, how to process that will need some further design consideration. The performance of each signer needs to be estimated and the liveness issue of a portion of the group should not impact the rewards on honest ones because it does not make much sense.
* The path of degraded of threshold level in the peg wallet degrades the security too. Normally security should not be the cost of the liveness.
* The recovery mode may wont work out. If reward value generated by the PoX protocol is much less than the value carried in the peg requests, the liveness is pretty low indeed.
* The on-chain price oracle. The white-paper mentions the oracle is fully on chain and decentralized, but without details for trust-worthy.

PowPeg by the RSK project is adopting HSMs to keep private keys away from the peg notaries. So, even the majority of signers wont have the ability to maliciously release the locked Bitcoins. According to the design, peg notaries need to run the PowHSM devices which are connected to RSK network(a Bitcoin merge-mining layer 2 chain), and the PowHSM only proceeds to sign a peg-out transaction upon receiving commands from the RSK blockchain, backed by 4000 confirmation blocks, with a cumulative proof-of-work currently equivalent to approximately 100 bitcoin blocks. In addition, if there's a censorship occurs by the peg functionaries, then the functionaries cannot continue to perform other peg-outs, as peg-outs are linked with UTXOs, and functionaries cannot choose the UTXOs for the peg-out transactions. The peg-out UTXOs, including “change” UTXOs, are selected by the Bridge contract, forming a consensus-enforced chain. With the trust assumption on these HSMs, and even extra attestation against the firmware running in them, the peg system becomes less risky than the old federated system solution. So good properties it argues includes:

* Peg notaries can not maliciously operate the locked Bitcoin, even with a majority.
* Proof of work verification in HSMs brings more security against malicious peg out requests.
* Censorship resistance on peg-outs with linked UTXOs.

Though it's still facing some factors of flaw or potential weakness.

* Trust-worthy HSM may not be transparent as they think. HSM involves not just the firmware, also the physical hardware. This brings extra security assumptions.
* These HSMs are hosted by the peg notaries and connected to a RSK node. How is the firmware update managed and the HSM's trust assumption on the RSK node needs to be audited and verified. Without knowing the details, there might be some potential risks.
* The peg notary group shift needs to go through a vote phase, that means the group composition change needs the permission of the current peg notaries, so not fully permissionless.

The designer of concept drivechains submitted a BIP to introduce a trust-minimized peg system for side chains. However it requires changes on the base chain, which looks still far from materialization. Here it's not in the discussion.

It's hard for the 2-way peg system to achieve full decentralization and safe enough without losing liveness. Extra safety assumption should be avoided when possible. However the common ideas are clear somehow. Like the stake or collateral to provide the safety in a system. It works well in the Ethereum PoS consensus and MakerDao's DAI system. The idea is that it's hard to ensure every part to behave 100% honestly, but we can ensure it malicious behave at enough cost to keep the entire system still live well. Here, I propose a potential solution of a novel 2-way Bitcoin Peg system between the base chain and a layer 2 chain.



<figure><img src="https://lh5.googleusercontent.com/lg0zITunH2I9SV1VEcIFHNWe1lqGzuqcwK338lm0Z8e4Sch67jwaCjlddKBj_XeB1OLdoJsgOcvFegT1XE830BvOiSNiQFYnYx6wI5n0mQDUNIq1s7vdfB2CloOR3M8rSjxdgxvTQRxz0Ni-DiiBnwU" alt=""><figcaption><p>Decentralized Peg System Design</p></figcaption></figure>

In this proposal, there's a bridge smart contract pre-deployed on layer 2 chains. This smart contract defines the stake/collateral logic. So anyone can stake layer 2 tokens into this contract to apply to be a peg operator. This process is fully permissionless, requiring no one's permission to achieve that. A collateral ratio F is defined to ensure the value of collateral is higher enough than the value of the locked Bitcoin a operator can hold. So if a peg-in request which results in the ratio passes the F will mark this operator as fully used, thus no more available for new peg-ins. Another specified ratio LF indicates a liquidation threshold, so when the collateral ratio goes above this ratio, likely caused the price change, a liquidation will be initialized by the bridge contract, which will open an order in the contract that any user can burn same amount of peg-BTC as held by the operator to get the full collateral. The liquidation happens too if the operator is maliciously move the fund he holds in the peg wallet on the Bitcoin chain no matter what's the current ratio of the operator. With such a mechanism there's hedging between selling the layer 2 tokens(the collateral) for BTC and burning peg-BTC for layer 2 tokens, while the whole process is fully open and decentralized.&#x20;

Mechanism in details:

* Anyone can apply to be a Peg operator by staking layer 2 token into bridge stake contract. This process does not need any permission as long as it's a valid operation verified by the protocol.
* Operator staked layer 2 token of value X, have an allowed peg amount of BTC of value X \* F (F \~= 70%?, F is the ratio between collateral  and the Bitcoin held). If a peg-in request results in the ratio passes F, this request will mark this operator as fully used. No further peg-in request can be handled by this operator without filling with more collateral.&#x20;
* Operator should keep an eye on the status of collateral to ensure the value is always enough so that the ratio wont go above LF(LF \~= 85%? LF is the ratio where a liquidation will be initialized).  Liquidation could be triggered by price change or malicious behavior of the operator which attempts to operate the received BTC(UTXOs) in peg wallet. Liquidation could be implemented in the bridge contract, like open a order which allows any user to burn same amount of peg-BTC to exchange for the full operator collateral. Liquidation also helps to maintain the pegged token to be 1:1 as the locked BTC. Liquidation request could be filled by arbitrators with a potential profit of 1-LF(\~= 15% of the volume). Liquidation could also be implemented with dexs on layer 2 chains.
* When a user wants to peg in some BTC, he can find the best operators by calling the bridge contract with the amount as parameter, so the contract will find the best set of operators to fill this request. Then the user transfers the BTC to the bound peg wallets of these operator aligning to the wire format defined in the protocol.
* When a layer 2 block producer derive the Bitcoin block which includes the peg-in request into a layer 2 block, it parses this transaction and  interpret it to be a peg-in transaction and mint the same amount of peg-BTC on layer 2 chain to the peg receiver..
* When a user wants to peg out BTC, he can send the peg-out request to bridge contract or broadcast a peg-out request in defined wire format on Bitcoin. Then block producers will parse the request same way as peg-ins. Then the contract when possible will select the best operator set to fill the request. This process will burn the peg-BTC of amount requested by the user, the chosen operators should process this peg-out request within N(N is Bitcoin blocks as a period for the operator to submit the peg out transfer transaction on Bitcoin chain) following Bitcoin blocks after enough confirmations on layer 2 chains for finality insurance.&#x20;
* When an operator makes invalid BTC transfer, or doesn't process the peg out requests in time. The staked layer 2 token(the collateral) will be distributed into users proportionally who requested to peg out with this operator, and the rest of collateral to be processed as liquidation.
* An operator could apply to exit, by sending a cease request into the bridge contract. A ceased status indicates the operator will not be able to process peg out in L days or Blocks. Users should not begin any further peg request with this operator. (L is the delay before a operator becomes available to exit, it's in ceased status during the delay).
* Operators in Ceased status, after L days/blocks, will be available to exit if his Bitcoin peg wallet holds no more Bitcoin, or he must transfer the balance into other operators who has staked enough amount of collateral. The transfer transaction should align to the defined wire format by the protocol, and should be broadcasted on the Bitcoin chain.
* The peg operator should receive enough rewards from either the protocol consensus or from the peg users, to cover the cost of running services, also as incentive to operate honestly with collateral.

This roughly composes such a 2-way peg system for Bitcoin, which has some desired properties like:

* Fully Open membership. Anyone can join the peg system as operators and free to leave as long as the operation is aligned to the protocol. It's fully permissionless.
* Decentralized. No centralized parties exist, only the protocol controls the system.
* Transparent. All peg-ins/peg-outs request are handled transparently on chain.&#x20;
* Censorship resistance. Both the peg-in request and peg-out request can be initialized from the base chain the Bitcoin chain to ensure the censorship resistance.
* Safety. Collateral with value of layer 2 token and a feasible ratio will ensure that malicious operator will be slashed with liquidation.&#x20;
* Liveness. With defined liquidation critiral, operator will have to fill the peg-out requests with in limited time period. Otherwise, the operator will be slashed on the collateral, also forfeiting the reward incentive from the consensus. High volume of peg requests will be splitted to a set of operators to provide enough availability.



Though there're still some potential flaws or weakness in my proposal.

* The collateral ratio estimation will depends on a price oracle, which introduces an extra security assumption. A fully decentralized price oracle is somehow hard to be implemented, thus a price oracle is hard to be fully trust-worthy.
* The collateral ratio makes less monetary efficiency. A supply of $100M of peg-BTC will require a collateral of layer 2 token with a value of $142M when we define the ratio F as 70%.





This proposal is still in draft status. Reviews are desired.
