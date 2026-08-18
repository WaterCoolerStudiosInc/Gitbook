---
description: >-
  This is the core contract for the protocol, and implements the Vault (a.k.a.
  the Kintsu stake pool).
icon: code
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/MdICYXISKGqWUQXbHV5x/the-raa-protocol/architecture-and-integration/stakedstarknet-contract
---

# StakedMonad Contract

## Overview

The `StakedMonad` contract - also known as the "vault" or the "Kintsu stake pool" - sits at the heart of the Kintsu Liquid Staking process. This contract is the user-facing entry point to the Kintsu Protocol, and serves a number of functions:

1. **Core functionality:** The `StakedMonad` contract is the core contract of the sMON token. It is an ERC-20 token contract with extended functionality.
2. **User entry point:** The `StakedMonad` contract is the user-facing interface to the protocol. Users who want to participate in staking yield can deposit MON to the `StakedMonad` contract and receive sMON tokens in return. Users can also request to redeem their sMON for staked MON along with their pro-rata share of the yield accrued by the protocol.&#x20;
3. **Orchestrates staking delegation & redemption:** The `StakedMonad` contract delegates staking tokens to, and interfaces with, [Validators](../../../overview/proof-of-stake-blockchains.md#staking-and-validators), according to the [Target Weights](stakedmonad-contract.md#target-weights) . This is done using Kintsu's [Constant Retargeting Algorithm](staking-and-un-staking-mechanisms.md#constant-regargeting-algorithm).
4. **Calculates Protocol Fees:** The `StakedMonad` contract calculates and stores [Virtual Shares](../../definitions.md) used to facilitate protocol Management Fees. For more information, see the docs on Governance & Management Fees.
5. **Inherits the Registry Interface:** The `Registry` interface maintains a list of [Validators](../../../overview/proof-of-stake-blockchains.md#staking-and-validators) that are actively participating in the Kintsu protocol, along with a set of Target Weights for allocation of staked MON to those Validators.&#x20;

{% hint style="info" %}
Note: the Redemption Ratio is not stored as a variable, however the `StakedMonad` contract contains methods to calculate it in both directions (from sMON to MON, and vice versa).
{% endhint %}

### Registry Interface Variables

#### Target Weights

These weights represent the target percentage of the total amount of staked MON allocated to each Validator. These target weights are meant to uphold decentralization of the protocol, and will be controlled by Governance in a decentralized way.

