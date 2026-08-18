---
icon: globe
metaLinks:
  alternates:
    - /broken/spaces/MdICYXISKGqWUQXbHV5x/pages/NGwUOCKli2hwgD30Tnti
---

# Community Actions

## Compounding

Hyperliquid staking protocol auto-compounds the accrued yield on HyperCore. To reflect this change in the Vault (on HyperEVM) and thereby update the [Redemption Ratio](../../definitions.md), Anyone can execute the [syncStaking()](contract-interface-abi-and-functions.md#condensed-stakedhype-contract-interface) call to sync the state with HyperCore.

{% hint style="info" %}
The Vault uses the `L1Read` module to read HyperCore state from the HyperEVM.
{% endhint %}

## Submit Batch

Anyone can execute the [submitBatch()](contract-interface-abi-and-functions.md#condensed-stakedhype-contract-interface) call to send the pending stake/unstake request(s) stored in the Vault to the HyperCore for processing.&#x20;

{% hint style="info" %}
The Vault uses the `CoreWriter` module for HyperCore <> HyperEVM interaction.
{% endhint %}

## Transfer spot balance from HyperCore to Vault

Anyone can execute the [sweep()](contract-interface-abi-and-functions.md#condensed-stakedhype-contract-interface) call to transfer the unstaked funds that have completed the unbonding cooldown period from HyperCore to the Vault (on HyperEVM).
