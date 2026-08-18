---
icon: infinity
metaLinks:
  alternates:
    - /broken/spaces/MdICYXISKGqWUQXbHV5x/pages/CylA5UcmjXSCihoeNr5Q
---

# Hyperliquid LST Architecture

## Protocol Architecture on Hyperliquid

The Kintsu Protocol operates as a series of interconnected Smart Contracts deployed on the blockchain. Each of these contracts serves a specific function of the protocol.

## Core Protocol Smart Contracts

The core smart contracts that make up the Kintsu protocol are the `StakedHype` Contract (manages the Vault and the Validator registry) and the `sHYPE` contract (ERC-20 token). The function of the `StakedHype` contract is detailed in the following page:

{% content-ref url="stakedhype-contract.md" %}
[stakedhype-contract.md](stakedhype-contract.md)
{% endcontent-ref %}

## **Staking and Un-staking on Hyperliquid**

These are the mechanisms behind the core actions available to protocol users. In addition, the protocol itself executes certain processes behind the scenes in order to fulfill the desired functionality.

{% content-ref url="staking-and-un-staking-mechanisms.md" %}
[staking-and-un-staking-mechanisms.md](staking-and-un-staking-mechanisms.md)
{% endcontent-ref %}

## Integration Guide

{% content-ref url="3rd-party-integration-guide.md" %}
[3rd-party-integration-guide.md](3rd-party-integration-guide.md)
{% endcontent-ref %}

## StakedHype Interface

A condensed interface containing the most important functions for integrating with the Kintsu core protocol on Hyperliquid.

{% content-ref url="contract-interface-abi-and-functions.md" %}
[contract-interface-abi-and-functions.md](contract-interface-abi-and-functions.md)
{% endcontent-ref %}

## **Community Actions**

These are actions that are necessary for the protocol to function as designed, and any community member can execute them. They may offer incentives to community members to assist in running the protocol. They do not require staking or un-staking of individual tokens.&#x20;

{% content-ref url="community-actions.md" %}
[community-actions.md](community-actions.md)
{% endcontent-ref %}
