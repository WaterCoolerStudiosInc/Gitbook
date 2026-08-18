---
icon: hive
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/MdICYXISKGqWUQXbHV5x/overview/proof-of-stake-blockchains
---

# Proof-of-Stake Blockchains

## Background

Many modern blockchains incentivize users to secure the blockchain via a consensus mechanism called Proof-of-stake (PoS). The native currency of these blockchains is called their native Gas Token, and they are used to execute transactions on the blockchain. For example, on Monad, MON is the gas token that powers the network. The native staking of MON is what creates a decentralized and secure network.

As with any blockchain, all transactions have to be validated by a decentralized network of third party participants in a way that incentivizes all parties involved to uphold the integrity of those transactions

### Staking and Validators

In the Proof-of-stake paradigm, users can lock their own Gas Tokens up and use them as collateral while they validate transactions on the blockchain. This process is known as transaction _**validation**_, and those who practice validation are called _**validators**_.&#x20;

In order to qualify to validate transactions, one must put up their own Gas Tokens as collateral - a process known as _**staking**_. These participants provide a critical role in securing the integrity of the blockchain. In return for this service, validators are rewarded with new Gas Tokens that are created by the blockchain as transactions are validated. Validators can therefore earn yield on their tokens that they’ve staked. However, if other participants on the network detect that the validator has asserted a transaction incorrectly or disingenuously, that validator forfeits the Gas Tokens that they staked. This mechanism keeps the validators honest.

This is an over-simplified explanation, and more information on Proof-of-stake can be found [<mark style="color:blue;">on Wikipedia</mark>](https://en.wikipedia.org/wiki/Proof_of_stake).

## Proof-of-Stake Inefficiencies

Staking provides a native mechanism for earning yield on the network’s native Gas Token. As a result, it provides one of the lowest risk ways for network participants to earn yield. This makes native staking yield the effective “risk-free rate” of the network with respect to the network’s Gas Token. This presents a few challenges:

1. **Staking minimums:**
   1. Many Proof-of-stake networks require a minimum staked amount for validators to participate in validating transactions. This creates a barrier to entry for those who hold smaller amounts of Gas Tokens, as many participants don’t have enough Gas Tokens to qualify to earn staking yield.
   2. Even when participants do have enough Gas Tokens to participate in theory, in many cases, there is an opportunity cost to staking, as locking up Gas Tokens precludes them from use in other network activities such as DeFi.
2. **Liquidity:** Any Gas Token locked up in consensus staking is unavailable for simultaneous use in other activities on the network. Thus, the network’s native consensus mechanism creates a powerful incentive for participants to leave their Gas Tokens staked, which limits the circulating quantity of the Gas Token available for use in network activity.

## Delegated Proof of Stake (DPoS)

Delegated Proof-of-Stake (DPoS) networks allow any user to delegate their tokens to one or more Validators on-chain. The user can request that the validator un-stake and return the tokens to them. The blockchain itself enforces the integrity of this mechanism. This mechanism is known as "Delegation".

## Staking at the Network Level

Each [era](../the-kintsu-protocol/definitions.md) on the blockchain, yield rewards in the form of new Gas Tokens are paid out to Validators. These rewards must be claimed, and sit in an unclaimed state on the blockchain until a Validator claims them. At any given time, there can only be a certain number of Validators that participate in staking validation, so the era’s yield is distributed equally to those Validators. Each Validator distributes its yield pro-rata to all Delegation Pools (Nomination Pools) who delegated (nominated) to it. This is done at the network level, so Validators don’t have a choice in whether they distribute the proceeds back to the Nomination Pool.

Validators can take a commission fee for their service. Each Validator sets their own commission. Delegation Pools (Nomination Pools) may delegate (nominate) to certain Validators over others due to a more competitive commission fee.

Gas Tokens sit in an unclaimed state at the network level until someone claims them and re-invests (re-delegates, or re-nominates) them.

### Cooldown Period

Blockchain networks may enforce a delay between the time that a Validator themself executes an [`unbonding`](../the-kintsu-protocol/definitions.md) call, and when the network releases the tokens to that Validator. This is called a "Cooldown Period", and serves to, among other things, disincentivize Validators from constantly bonding and unbonding (staking and unstaking) Gas Tokens, which may clog up the network. This cooldown period may vary from network to network. You can find information about each network's Cooldown Period, if applicable, in that network's page in Kintsu's docs.

### Daily Unbonding Request Limits

Furthermore, the network itself may limit the number of unbonding (unstaking) requests that a Validator can make per day, for similar reasons behind the Cooldown Period.
