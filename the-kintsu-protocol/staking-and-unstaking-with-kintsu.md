---
description: How to Stake, Un-stake, and earn yield with Kintsu
icon: money-bill-transfer
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/MdICYXISKGqWUQXbHV5x/the-raa-protocol/staking-and-unstaking-with-raa
---

# Staking and Unstaking with Kintsu

## Staking with Kintsu

When a user stakes tokens with the Kintsu protocol, the experience is as follows:

1. User initiates a `stake` transaction, in which they submit Gas Tokens (e.g. MON or HYPE, depending on the connected network) to the Kintsu staking pool (a.k.a. the Vault).
2. User receives sTokens as receipt tokens. The amount received depends on the current [Redemption Ratio](definitions.md), which can be seen on the Kintsu website beforehand.

{% hint style="info" %}
For example, if the current Redemption Ratio is 1.1 GAS for every sGAS, then a user who stakes 11 GAS will receive 10 sGAS from the Vault.
{% endhint %}

sTokens tokens are fungible, and can be traded on [DEXes](definitions.md) or used as a proxy for Gas Tokens in other activities on the blockchain, such as DeFi applications, Gaming, NFTs, and more.&#x20;

For more technical information on how staking works behind the scenes, see Kintsu's docs on the [**Staking Mechanism**](architecture-and-integration/monad-lst-architecture/staking-and-un-staking-mechanisms.md#staking-mechanism).



***

## Unstaking with Kintsu

Users can exchange their sTokens (e.g. sMON or sHYPE) for Gas Tokens (e.g. MON or HYPE) tokens from the protocol. The number of Gas Tokens received depends on the [Redemption Ratio](definitions.md) at that time, with respect to the number of sTokens they submit for redemption.&#x20;

{% hint style="info" %}
For example, if the current Redemption Ratio is 1.1 GAS for every sGAS, then a user who submits 10 sGAS will receive 11 GAS from the Vault.
{% endhint %}

It's important to note that when using Liquid Staking, there will be a delay between the time that a user requests to unstake sTokens and when they actually receive the Gas Tokens. There are a few reasons for this, including native unstaking delays built into, and enforced by, the blockchain itself. The user experience is as follows:

1. **Initiation:** User executes an unstaking request, specifying _either_ how many sTokens they'd like to submit, _or_ how many Gas Tokens they'd like to receive.
2. **Cooldown Period:** Once the unbonding request is sent to the Validators, the users must wait for the [Cooldown Period](../overview/proof-of-stake-blockchains.md#cooldown-period). This is enforced by the blockchain itself, and there is no way around it.&#x20;
3. **Redemption:** Once the Cooldown Period is over, the user can redeem their Gas Tokens to their wallet.

For more detailed technical information on how this process works, see Kintsu's docs on the [**Unstaking Mechanism**](architecture-and-integration/monad-lst-architecture/staking-and-un-staking-mechanisms.md#unstaking-mechanism)**.**

