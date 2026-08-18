---
description: Protocol Architecture & Functionality
icon: info
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/MdICYXISKGqWUQXbHV5x/the-raa-protocol/how-it-works
---

# How It Works

## Mission

Kintsu is a [Liquid Staking Protocol](../overview/liquid-staking.md). Our mission is to boost the GDP of Proof-of-Stake blockchains by allowing users to participate in on-chain activities while also benefitting from the yield-bearing staking that secures the blockchain. We do this by providing stakers with a liquid token which is backed by the blockchain's staked native gas tokens. We call this a Liquid Staking Token, abbreviated as `LST` .

{% hint style="info" %}
**Note:** Kintsu refers to its LSTs as "sTokens". For example, the Gas Token of Monad is MON, and the LST of Kintsu on Monad is sMON.
{% endhint %}

## How it works

Kintsu improves on [Delegated Proof-of-Stake](../overview/proof-of-stake-blockchains.md#delegated-proof-of-stake-dpos) by allowing users to participate without themselves having to find and choose a validator to which to delegate. To accomplish this, Kintsu pools users’ Gas Tokens together and delegates them across a set of participating network [Validators](../overview/proof-of-stake-blockchains.md#staking-and-validators) for staking, and gives those users sTokens (the collateral) that can be used to redeem their staked Gas Tokens at any time. The protocol maintains a decentralized list of participating validators, routes staking requests to them, and facilitates redemption requests on behalf of the user.



