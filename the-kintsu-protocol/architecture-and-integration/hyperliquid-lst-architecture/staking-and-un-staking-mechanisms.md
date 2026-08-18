---
description: What happens behind the scenes when a user stakes or unstakes with Kintsu
icon: gear-code
metaLinks:
  alternates:
    - /broken/spaces/MdICYXISKGqWUQXbHV5x/pages/PjBKbcv6lOto8t63RdOe
---

# Staking and Un-staking Mechanisms

## **Staking Mechanism**

Staking on Kintsu involves the following order of operations:

1. **HYPE Deposit:** User deposits HYPE into the [`StakedHype`](stakedhype-contract.md) contract by initiating a `deposit()` transaction.
2.  **Receipt Token Issuance:** The `StakedHype` contract calculates the number of new shares (sHYPE) to create, using the current [Redemption Ratio](../../definitions.md), and transfers them to the user. These sHYPE serve as a receipt entitling the holder to their share of the [Total Pooled](../../definitions.md). The sHYPE tokens are fungible, can be traded and used elsewhere on the network, and can be used to redeem HYPE during the unstaking process. The number of new sHYPE created, denoted here as _newShares,_ is as follows:<br>

    $$newShares = newStake  * (totalShares/totalPooled)$$ <br>

    where _`newStake`_ is the number of newly deposited HYPE, _`totalShares`_ is the [Total Shares](../../definitions.md) and _`totalPooled`_ is the [Total Pooled](../../definitions.md).
3. **Request is added to Current Batch**:  The user's deposit request is added to the current batch, to be submitted to validators.

To see how this translates into a user's experience staking on Kintsu, see our docs on [Staking with Kintsu](../../staking-and-unstaking-with-kintsu.md#staking-with-kintsu).

## Unstaking Mechanism

Unstaking on Kintsu involves a two step execution by the user - `Request Unlock` and `Redeem Request`. The complete lifecycle of unstaking is as follows:

1. **Initiation:** User calls [requestUnlock()](contract-interface-abi-and-functions.md#condensed-stakedhype-contract-interface), during which they submit their sHYPE to the Vault.
2. **Spot value is determined:**  The `StakedHype` contract calculates the number of HYPE to return to the user, using the current [Redemption Ratio](../../definitions.md) after applying a nominal exit fee of `0.05%` (unless exempted).&#x20;
3. **Request is added to Current Batch**:  The user's unlock request is added to the current batch, to be submitted to validators.
4. **Cooldown Period:** User must wait until [Cooldown Period](../../../overview/proof-of-stake-blockchains.md#cooldown-period) is over so that Validators are able to unbond the HYPE tokens.
5. **Tokens returned to Vault:** Once the cooldown period has ended, Anyone can call [sweep()](community-actions.md#transfer-spot-balance-from-hypercore-to-vault) to transfer all the newly unbonded HYPE held in the HyperCore back to the Vault (on HyperEVM).
6. **Token redemption:** This is where the user can redeem their HYPE, including accrued yield. User makes a [redeem()](contract-interface-abi-and-functions.md#condensed-stakedhype-contract-interface) call to the Vault, which sends the user's newly unstaked HYPE to their wallet.

To see how this translates into a user's experience staking on Kintsu, see our docs on[ Unstaking with Kintsu](../../staking-and-unstaking-with-kintsu.md#unstaking-with-kintsu).

{% hint style="info" %}
Note: Users can cancel their unstaking request anytime before their batch is executed by calling [cancelUnlockRequest()](contract-interface-abi-and-functions.md#condensed-stakedhype-contract-interface)
{% endhint %}

***

### Batch submission

The vault aggregates the deposit and unlock requests added in the current batch and then uses the [Constant Retargeting Algorithm](staking-and-un-staking-mechanisms.md#constant-retargeting-algorithm) to calculate how many HYPE to stake with each validator (in the case of net-inflow) OR how many HYPE to unstake from each validator (in the case of net-outflow). Finally, the request is sent to the Hyperliquid staking module on HyperCore using the `CoreWriter` precompile.

Anyone can initiate the [submitBatch()](contract-interface-abi-and-functions.md#condensed-stakedhype-contract-interface) call to the `StakedHype` contract.

{% hint style="info" %}
Bonus: When the net outflow is zero, The unlock request(s) of that batch does not need to wait for the network induced cooldown period and they can immediately redeem their unlock request 🪄✨
{% endhint %}

### Constant Retargeting Algorithm

As the [Vault](stakedhype-contract.md) accepts new HYPE for staking, it must delegate them across the participating Validators in such a way that aims to keep the amount delegated to each Validator consistent with the [Target Weights](stakedhype-contract.md#target-weights) stored in the [Registry](stakedhype-contract.md#registry-interface-variables). In order to do this, the Vault retrieves the Target Weights from the Registry and compares them to the current percentages of the Total Pool held by each of the participating Validators at that time. The Vault then calculates how much of the newly added HYPE should be delegated to each Validator in order to bring the current weights closer to the Target Weights stored in the Registry.

During the unstaking process, the Vault again uses the Target Weights from the Registry to decide how many HYPE to redeem from each Validator such that the current weights converges to the Target Weights stored in the Registry.
