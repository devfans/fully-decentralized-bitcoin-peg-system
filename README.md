---
description: >-
  How much decentralization can a Bitcoin 2-way peg system achieve? A fully
  decentralized 2-way peg system.
---

# fully-decentralized-bitcoin-peg-system

Scaling the Bitcoin chain with side chains or layer 2 chains is considered as one of the potential solutions to gain more transaction throughput and extend the functionality. To build such a chain, a valid 2-way peg system is required to ensure that Bitcoin is freely and easily pegged from the base chain to the side chains or layer 2 chains and pegged back when needed, we call the latter layer 2 chains in the following context. So we commonly consider how to design such a peg system. I did some research on how sBTC by the "sBTC Working Group" works, and the design of PowPeg by the RSK project, and analyze the tradeoffs each made to bring decentralization and safety to the 2-way peg systems, and then I am proposing a potential new way to gain more decentralization for a peg system, a fully decentralized Bitcoin Peg.

Before that, we need to have a clear definition for such a valid 2-way peg system. Here I am describing the validation with three factors accompanied by some explanations.

* Full Decentralization. Decentralization is the main characteristic brought by Satoshi Nakamoto in the Bitcoin design. Centralized parties, authorities, federations bring potential risks to a system, as the system relies on trusting these parties and there is not a novel way to make a party fully trust-worthy. Here we say full decentralization, because there're some extra properties a system should have.
  * Transparency. A decentralized system might be operated by a group of members. The way of how such a group operate the system and how the group members are selected should be transparent. The operation target might be privacy protected, but the mechanism should be transparent. So any party can verify against it and then make a decision to endorse or disagree.
  * Open Membership. Anyone or any party should be able to join or leave such a system freely when it's a valid operation. It should be permissionless. For example, if a system is operated by a group of signers selected in a decentralized way, however, a new signer joins such a system with requiring the permission of the current signer group. It's hence not a fully decentralized system. Open membership protects any party's right of access to such a system. it's important to avoid censorship.
* Safety. Safety serves as the insurance on the value owned by system users. Consider a peg system is operated by a group of signers using a multi-sig wallet, and the group of signers have no bonds or limitations from the system protocol. Then they are free to maliciously operate the locked Bitcoin on the base chain. As a result, the peg token on the layer 2 chains will be valueless and peg users have to take the loss due to the lack of safety.
* Liveness. A peg system should be designed to ensure sufficient availability and usability. Even if a system is designed as fully decentralized and safe enough, but, however, the cost to operate such a system is very high. This might makes the system unable to run due to lack of operators. Thus, the liveness is too low. The efficiency of the monetary liquidity should be taken into the consideration when designing such a system. The system is desired to stay operable even in extreme and unliked circumstances.

Why building such a valid 2-way peg system is challenging on Bitcoin? The Bitcoin chain is widely regarded as the most decentralized, secure and longstanding blockchain. However, another property of the chain is the simplicity, it lacks the full expressive smart contracts, even nowadays most of the newly created chains support such a feature. This does not indicate it's bad though since functionality extension might come with extra security risk. However, this limitation does restrict the implementation of a decentralized 2-way peg system. In contrast, a decentralized 2-way peg system is not as challenging to design for Ethereum based layer 2 chains.

sBTC designed by the "sBTC Working Group" combines the consensus of its layer 2 chain with its peg system through a stacker role. Layer 2 chain miners need to bids on the block producer role by transferring BTC to the stackers. Stackers are a group of signers who stake their layer 2 token (STX) to operate the peg system, and the value of the locked layer 2 tokens should be higher than they can potentially steal from the peg wallet. In this design, the threshold level of the peg wallet and the value of locked layer 2 tokens provides the safety of the systems(Note: Penalties with the locked layer 2 token are not mentioned in the white-paper indeed). Additionally, the economic incentive compatibility provides the liveness. Some good properties it has indeed.

* Anyone can elect to be a stacker by following the protocol and staking their layer 2 tokens.
* Compromising the peg wallet needs the consensus of the group to reach a threshold level. Normally it's hard to achieve this if it's designed as not incentivized.
* Economic incentive gives the peg system operator rationale to behave honestly.
* The pegging requests are broadcasted on the base chain, to gain the censorship resistance from the Bitcoin chain.
* Recovery mode to keep the liveness when the threshold level is not met.

However, when think carefully, there might be some flaws as below.

* Open membership might not be that open as said. Shifting the signers requires the consensus of the current signer group. If group A gains sufficient benefits to be convinced to deny the change to the share of them, thus the membership shift might be hard. So membership is not fully permissionless.
* How to treat the locked layer 2 tokens owned by the stackers? The white paper does not mention the slashing or how to slash. At the worst case which has no slashing on stackers, the group will be incentivized to collude in stealing the Bitcoin in peg wallets. If there's slashing with these locked layer 2 tokens, how to process that will need some further design consideration. The performance of each signer needs to be estimated and the liveness issue of a portion of the group should not impact the rewards on honest ones, as it would be illogical.
* The script path of degradation on threshold level in the peg wallet degrades the security too. Normally security should not be the cost of the liveness.
* The recovery mode may won't work out. When reward value generated by the PoX protocol is significantly less than the value carried in the peg requests, the liveness would be quite low indeed.
* The on-chain price oracle. The white-paper mentions the oracle is fully on chain and decentralized, but without sufficient details for trust-worthy.

PowPeg by the RSK project is adopting HSMs to keep private keys away from the peg notaries. So, even the majority of signers won't have the ability to maliciously release the locked Bitcoins. According to the design, peg notaries need to run the PowHSM devices which are connected to RSK network(a Bitcoin merge-mining layer 2 chain), and the PowHSM only proceeds to sign a peg-out transaction upon receiving commands from the RSK blockchain, backed by 4000 confirmation blocks, with a cumulative proof-of-work currently equivalent to approximately 100 bitcoin blocks. In addition, if there's a censorship occurs by the peg functionaries, then the functionaries cannot continue to perform other peg-outs, as peg-outs are linked with UTXOs, and functionaries cannot choose the UTXOs for the peg-out transactions. The peg-out UTXOs, including “change” UTXOs, are selected by the Bridge contract, forming a consensus-enforced chain. With the trust assumption on these HSMs, and even extra attestation against the firmware running in them, the peg system becomes less risky than the old federated system solution. So good properties it argues includes:

* Peg notaries can not maliciously operate the locked Bitcoin, even with a majority.
* Proof of work verification in HSMs brings more security against malicious peg out requests.
* Censorship resistance on peg-outs with linked UTXOs.

Though it's still facing some factors of flaw or potential weakness.

* Trust-worthy HSM may not be as transparent as they think. HSM involves not just the firmware, also the physical hardware. This brings extra security assumptions.
* These HSMs are hosted by the peg notaries and connected to a RSK node. How is the firmware update managed and the HSM's trust assumptions on the RSK node needs to be audited and verified. Without knowing the details, there might be some potential risks.
* The peg notary group shift needs to go through a vote phase, that means the group composition change needs the permission of the current peg notaries, so not fully permissionless.

The designer of concept drivechains submitted a BIP to introduce a trust-minimized peg system for side chains. However it requires changes on the base chain, which looks still far from materialization. Here it's not in the discussion.

It's hard for the 2-way peg system to achieve full decentralization and be safe enough without losing liveness. Extra safety assumption should be avoided when possible. However the common ideas are clear somehow, such as using the stake or collateral to provide the safety in a system. It works well in the Ethereum PoS consensus and MakerDao's DAI system. The underlying idea is that it's hard to ensure every party behaves 100% honestly, but we can ensure it malicious behave at enough cost to keep the entire system still live well. Here, I propose a potential solution of a novel 2-way Bitcoin Peg system between the base chain and a layer 2 chain.



<figure><img src="https://lh5.googleusercontent.com/lg0zITunH2I9SV1VEcIFHNWe1lqGzuqcwK338lm0Z8e4Sch67jwaCjlddKBj_XeB1OLdoJsgOcvFegT1XE830BvOiSNiQFYnYx6wI5n0mQDUNIq1s7vdfB2CloOR3M8rSjxdgxvTQRxz0Ni-DiiBnwU" alt=""><figcaption><p>Decentralized Peg System Design</p></figcaption></figure>

In this proposal, there's a bridge smart contract pre-deployed on layer 2 chains. This smart contract defines the stake/collateral logic. So anyone can stake layer 2 tokens into this contract to apply to be a peg operator. This process is fully permissionless, requiring no one's permission to achieve that. A collateral ratio F is defined to ensure the value of collateral is higher enough than the value of the locked Bitcoin an operator can hold. So a peg-in request which results in the ratio passes the F will mark this operator as fully utilized, thus no more available for new peg-ins. Another specified ratio LF indicates a liquidation threshold, so when the collateral ratio goes above this ratio, likely caused the price change, a liquidation request will be initiated by the bridge contract, which will open an order in the contract that allowing any user to burn same amount of peg-BTC as BTC held by the operator to receive the full collateral. The liquidation happens too if the operator is maliciously moving the fund he holds in the peg wallet on the Bitcoin chain regardless of the current ratio of the operator. With such a mechanism there's hedging between selling the layer 2 tokens(the collateral) for BTC and burning peg-BTC for layer 2 tokens, while the whole process is fully open and decentralized.&#x20;

Mechanism in details:

* Anyone can apply to be a Peg operator by staking layer 2 token into bridge stake contract. This process does not need any permission as long as it's a valid operation verified by the protocol.
* Operator staked layer 2 token of value X, have an allowed peg amount of BTC of value X \* F (F \~= 70%?, F is the ratio between collateral  and the Bitcoin held). If a peg-in request results in the ratio passing F, this request will mark this operator as fully utilized. No further peg-in requests can be handled by this operator without filling with more collateral.&#x20;
* Operators should keep an eye on the status of collateral to ensure the value is always sufficient so that the ratio wont go above LF(LF \~= 85%? LF is the ratio where a liquidation request will be initialized).  Liquidation can be triggered by price change or malicious behavior of the operator which attempts to operate the received BTC(UTXOs) in a peg wallet. Liquidation could be implemented in the bridge contract, like opening an order which allows any user to burn the same amount of peg-BTC in exchange for the full operator collateral. Liquidation also helps to maintain the pegged token to be 1:1 as the locked BTC. Liquidation requests can be fulfilled by arbitrators with a potential profit of 1-LF(\~= 15% of the value volume). Liquidation could also be implemented using decentralized exchanges(DEXs) on layer 2 chains.
* When a user wants to peg in some BTC, he can find the best operators by calling the bridge contract with the desired amount as a parameter. The contract will determine the best set of operators to handle this request. Then the user transfers the BTC to the bound peg wallets of these operators aligning to the wire format defined in the protocol.
* When a layer 2 block producer derives the Bitcoin block which includes the peg-in request into a layer 2 block, it parses this transaction and interprets it to be a peg-in transaction and mint the same amount of peg-BTC on layer 2 chain to the peg receiver..
* When a user wants to peg out BTC, they can send the peg-out request to bridge contract or broadcast a peg-out request in defined wire format on Bitcoin. Then block producers will interpret the request same way as peg-ins. Then the contract when possible will select the best operator set to fill the request. This process will burn the peg-BTC of amount requested by the user, the chosen operators should process this peg-out request within N(N is Bitcoin blocks as a period for the operator to submit the peg out transfer transaction on Bitcoin chain) following Bitcoin blocks after enough confirmations on layer 2 chains for finality assurance.&#x20;
* When an operator made invalid BTC transfers, or failed to process the peg out requests in time. The staked layer 2 token(the collateral) will be distributed to users proportionally who requested to peg out with this operator, and the rest of collateral will be processed through liquidation.
* An operator could apply to exit, by sending a cease request into the bridge contract. A ceased status indicates the operator will not be able to process any peg in requests in L days or Blocks. Users should not initiate any new peg-in requests with this operator while pegging out is not impacted. (L is the delay before an operator becomes available to exit, it's in ceased status during the delay).
* Operators in ceased status, after L days/blocks, will be available to exit if his Bitcoin peg wallet holds no remaining Bitcoin. Alternatively, they can transfer the balance to other operators who have staked enough collateral. The transfer transaction should align to the defined wire format by the protocol, and should be broadcasted on the Bitcoin chain.
* The peg operator should receive enough rewards from either the protocol consensus or the peg users, to cover the cost of running services, also as incentive to operate honestly with collateral.
* To process liquidation requests efficiently, any user should be allowed to fulfill these requests by interaction calls with the bridge contract and a single liquidation order can either be fulfilled as a whole or partially filled at a time. As an alternative solution, a shared liquidation order could be maintained, so new liquidation orders will be merged into it with an updated average price.
* When the price of layer 2 token against the Bitcoin goes down much faster than the speed of the process of liquidation and the speed of the operators to add more collateral, the worst situation might happen, in which, the liquidation occurs on all the operators. Assuming even we reached such a case, the remaining peg-BTC in layer 2 chains will always hold the value same as all the collateral under liquidation, no matter how far the price of the layer 2 token goes down. When the price of layer 2 token goes up again, the liquidation requests will be fully filled and the system will resume.  However, it's still unacceptable for such a peg to suffer this. An extra insurance should be set up to hold the bottom of these peg-BTC. A funding pool of layer 2 token can be reserved by the governance of the layer 2 chain to achieve this, so that when such an extreme situation happens, liquidation will still be fulfilled as the pool will compensate for the loss of the possible profits of arbitrators. After all the liquidation requests are fulfilled, the new peg-ins can continue and the system will resume even when the price of layer 2 tokens is still lower than before.&#x20;
* To protect operators from deny-of-service attacks or possible liquidation attacks, the peg-in process could be split into 3 steps with a hash time lock contract(HTLC) introduced. The first step is that the user who needs to peg in, needs to find the best operator set by calling the bridge contract, then set up the transfer transaction as described. However, in the locking script of the outputs of the transaction, there will be two script paths, so that the output value can either spent by the operator to move the funds by providing the preimage of the lock, or by the requester to release the funds after certain time(RL1) elapses. When this transaction is interpreted into the layer 2 chain, a mint transaction will be initiated if the operator set still are eligible to consume this peg-in without the ratio passing the threshold, otherwise the peg-in request will be rejected and the requester can release the funds on Bitcoin after the duration of RL1. But, if such a mint transaction is prepared, the user can either release the minted token by providing the pre-image to the bridge contract within time duration RL2, or cancel this request and release the funds on the Bitcoin chain after RL1 elapses. Operators should move the funds to their peg wallet on the Bitcoin chain with the preimage provided within RL2. Here, the RL2 must be much less than the RL1. The preimage can be some random bytes or a signature on it from the requester.

This roughly outlines such a 2-way peg system for Bitcoin, which has some desired properties like:

* Fully Open membership. Anyone can join or leave the peg system as operators freely as long as the operation is aligned to the protocol on a permissionless basis.
* Decentralized. No centralized parties exist, only the protocol controls the system.
* Transparency. All peg-ins/peg-outs requests are handled transparently on chain.&#x20;
* Censorship resistance. Both the peg-in request and peg-out request can be initiated from the Bitcoin chain to ensure the censorship resistance.
* Safety. Collateral with value of layer 2 token and a feasible ratio will ensure that malicious operators will be slashed with liquidation.&#x20;
* Liveness. With defined criteria for liquidation, operators will have to fill the peg-out requests within a specified timeframe. Otherwise, the operator will be slashed on the collateral, also forfeiting the reward incentive from the consensus. High volume of peg requests can be split among a set of operators to provide enough usability and availability.



Though there's still some potential flaws or weaknesses in my proposal.

* The collateral ratio estimation will depend on a price oracle, which introduces an extra security assumption. A fully decentralized price oracle is somehow hard to be implemented, thus a price oracle is hard to be fully trust-worthy.
* The collateral ratio makes less monetary efficiency. A supply of $100M of peg-BTC will require a collateral of layer 2 token with a value of $142M when we define the ratio F as 70%. In addition, an insurance pool is required to handle the extreme odd situations as described.



Both my proposal and the sBTC design involve the lock of layer 2 token. However, there're some obvious differences. Here's to describe the tradeoffs.

* There's a group of signers to manage the peg wallet in sBTC, while in my proposal, peg operators hold their own ones respectively. This difference leads to different properties on each other. sBTC signers need to reach a consensus on shifting the signer group to transfer funds to a new peg wallet. This indicates the membership change needs the permission of the previous group.  It sometimes can be challenging for a group of signers to reach such a consensus due to conflicts of interests or lack of enough liveness of signers. What it gains is obvious too. To compromise such a peg wallet will be hard when not incentivized. The single peg wallet in sBTC can also meet the need of peg-out requests when the volume is high.  While in my proposal, there's no such a group controlled wallet. Any operator can join or leave the system freely without the requirement of any permission. The tradeoff is that it's easy for a single operator to malicious behave as long as willing to bear the loss of the collateral and forfeit the rewards. For high volume peg-out requests, a set of operators can be chosen to fulfill the requirements, so without loss of usability.
* sBTC has a degraded script path in design to tolerate the offline signers and other situations when the target threshold level can not be met. The tradeoff is the degradation of security in exchange for extra liveness, while in my proposal, the liveness is ensured by the collateral held by operators which has a higher value than the BTC held in peg wallets and the reward incentives from the consensus or peg users. The reward incentives apply to sBTC too as specified in its design.
* In sBTC's design, the treatment of collateral is not clearly specified. While in my proposal, there's a clearly defined liquidation mechanism to slash misbehaving operators. At the worst case, without a clear slashing mechanism the signer group will be incentivized to collude to steal the money in peg wallet, even forfeiting the reward from the consensus protocol. The slashing mechanism, if defined, should be decentralized and transparent to provide the fairness and safety of the system.



This proposal is still in draft status. Reviews are desired.



Issues and PRs are welcome here: [https://github.com/devfans/fully-decentralized-bitcoin-peg-system](https://github.com/devfans/fully-decentralized-bitcoin-peg-system)



Thanks to [@Igor](https://github.com/igorsyl) for the first review and feedback.



Reference Links:



[https://stx.is/sbtc-pdf](https://stx.is/sbtc-pdf)

[https://dev.rootstock.io/rsk/architecture/powpeg/](https://dev.rootstock.io/rsk/architecture/powpeg/)

[https://dev.rootstock.io/rsk/architecture/security/](https://dev.rootstock.io/rsk/architecture/security/)&#x20;
