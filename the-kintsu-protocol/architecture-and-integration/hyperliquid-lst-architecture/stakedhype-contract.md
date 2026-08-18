---
description: >-
  This is the core contract for the protocol on HyperEVM, and implements the
  Kintsu Vault (a.k.a. the Kintsu stake pool on Hyperliquid).
icon: code
metaLinks:
  alternates:
    - /broken/spaces/MdICYXISKGqWUQXbHV5x/pages/HA0wH1vUUpcPBFW9TIDR
---

# StakedHype Contract

## Overview

The `StakedHype` contract - also known as the "vault" or the "Kintsu stake pool" - sits at the heart of the Kintsu Liquid Staking process. This contract is the user-facing entry point to the Kintsu Protocol, and serves a number of functions:

1. **User entry point:** The `StakedHype` contract is the user-facing interface to the protocol. Users who want to participate in staking yield can deposit HYPE to the `StakedHype` contract and receive sHYPE tokens in return. Users can also request to redeem their sHYPE for staked HYPE along with their pro-rata share of the yield accrued by the protocol.&#x20;
2. **Orchestrates staking delegation & redemption:** The `StakedHype` contract delegates staking tokens to, and interfaces with, [Validators](../../../overview/proof-of-stake-blockchains.md#staking-and-validators), according to the [Target Weights](stakedhype-contract.md#target-weights) . This is done using Kintsu's [Constant Retargeting Algorithm](staking-and-un-staking-mechanisms.md).
3. **Calculates Protocol Fees:** The `StakedHype` contract calculates and stores [Virtual Shares](../../definitions.md) used to facilitate protocol Management Fees. For more information, see the docs on Governance & Management Fees.
4. **Inherits the Registry Interface:** The `Registry` interface maintains a list of [Validators](../../../overview/proof-of-stake-blockchains.md#staking-and-validators) that are actively participating in the Kintsu protocol, along with a set of Target Weights for allocation of staked HYPE to those Validators.&#x20;

{% hint style="info" %}
Note: the Redemption Ratio is not stored as a variable, however the `StakedHype` contract contains methods to calculate it in both directions (from sHYPE to HYPE, and vice versa).
{% endhint %}

### Registry Interface Variables

#### Target Weights

These weights represent the target percentage of the total amount of staked HYPE allocated to each validator. These target weights are meant to uphold decentralization of the protocol, and will be controlled by Governance in a decentralized way.

