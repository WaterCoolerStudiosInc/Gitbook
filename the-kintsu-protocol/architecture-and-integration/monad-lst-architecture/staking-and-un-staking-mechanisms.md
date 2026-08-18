---
description: What happens behind the scenes when a user stakes or unstakes with Kintsu
icon: gear-code
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/MdICYXISKGqWUQXbHV5x/the-raa-protocol/architecture-and-integration/staking-and-un-staking-mechanisms
---

# Staking and Un-staking Mechanisms

## **Staking Mechanism**

Staking on Kintsu involves the following order of operations:

1. **MON Deposit:** User deposits MON into the [`StakedMonad`](stakedmonad-contract.md#the-vault-contract) contract by initiating a `stake` transaction.
2. **Validator Forwarding:** During stake, `delegateBondingto` is called, retrieving the current [Target Weights](stakedmonad-contract.md#target-weights). It then checks all participating Validators to see how many MON are currently allocated to each. These sums are then used to calculate the Current Weights (the percentage of the Total Pool held by each Validator). Finally the [Constant Retargeting Algorithm](staking-and-un-staking-mechanisms.md#constant-regargeting-algorithm)  is triggered to calculate how many of the newly submitted MON to forward to each of the Validators, and forwards them accordingly.
3.  **Receipt Token Issuance:** The `StakedMonad` contract calculates the correct number of new shares (sMON) to create, using the current [Redemption Ratio](../../definitions.md), and transfers them to the user. These sMON serve as a receipt entitling the holder to their share of the [Total Pooled](../../definitions.md). The sMON tokens are fungible, can be traded and used elsewhere on the network, and can be used to redeem MON during the unstaking process. The number of new sMON created, denoted here as _newShares,_ is as follows:<br>

    $$newShares = newStake  * (totalShares/totalPooled)$$ <br>

    where _newStake_ is the number of newly deposited MON, _totalShares_ is the [Total Shares](../../definitions.md) and _totalPooled_ is the [Total Pooled](../../definitions.md).

To see how this translates into a user's experience staking on Kintsu, see our docs on [Staking with Kintsu](../../staking-and-unstaking-with-kintsu.md#staking-with-kintsu).

### Constant Retargeting Algorithm

As the [Vault](stakedmonad-contract.md#staked-monad-contract-functions) accepts new MON for staking it must delegate them across the participating Validators in such a way that aims to keep the amount delegated to each Validator consistent with the [Target Weights](stakedmonad-contract.md#target-weights) stored in the [Registry](stakedmonad-contract.md#registry-interface-variables). In order to to do this, the Vault retrieves the Target Weights from the Registry and compares them to the current percentages of the Total Pool held by each of the participating Validators at that time. The Vault then calculates how much of the newly added MON should be delegated to each Validator in order to bring the current weights closer to the Target Weights stored in the Registry.

During the unstaking process, the Vault again uses the Target Weights from the Registry to decide how many MON to redeem from each Validator.



***

## Unstaking Mechanism

Kintsu involves some clever mechanisms to enable unstaking at scale, despite network-level constraints such as the Cooldown Period and [Daily Unbonding Request Limits](staking-and-un-staking-mechanisms.md#daily-unbonding-request-limits).&#x20;

### Unstaking Order of Operations

Unstaking on Kintsu involves the following order of operations:

1. **Initiation:** User calls [Request Unlock](contract-interface-abi-and-functions.md#unstaking-functions), during which they submit their sMON to the Vault.&#x20;
2. **Request added to Current Batch**:  The uses unlock request is added to the current batch of unlocks, to be submitted to validators.
3. **Batch Unlock Requests sent:** After the two era period for the current batch, the unlock requests are sent to validators. These requests leverage the [Constant Retargeting Algorithm](staking-and-un-staking-mechanisms.md#constant-retargeting-algorithm) to keep withdraws as evenly as possible.
4. **Cooldown Period:** User must wait until [Cooldown Period](staking-and-un-staking-mechanisms.md#cooldown-period) is over so that Validators are able to unbond the MON tokens.
5. **Tokens returned to Vault:** Once the cooldown period has ended, the first user who calls `redeem`, also triggers the `withdrawUnbonded` method on the `StakedMonad` contract (a.k.a. the Vault). This forwards all newly unbonded MON held by the Validators back to the Vault.
6. **Token redemption:** This is where the user can redeem their MON, including accrued yield. In order for this to happen, the Vault must have custody of the unbonded MON. There are two scenarios here:
   1. If the newly unbonded MON are still held by Validators when a user tries to redeem theirs, the `withdrawUnbonded` method is called under the hood, during which the `Vault` collects the unbonded MON from each of the Validator, and then executes the [`redeem`](contract-interface-abi-and-functions.md) call, which forwards the user's entitled portion of those MON from the `Vault` to the user's wallet.
   2. If the Vault already has custody of the MON, the user can make a [`redeem`](contract-interface-abi-and-functions.md) call to the `Vault`, which sends the user's newly unstaked MON to their wallet.

To see how this translates into a user's experience staking on Kintsu, see our docs on[ Unstaking with Kintsu](../../staking-and-unstaking-with-kintsu.md#unstaking-with-kintsu).

### Request Routing

In order for the user to receive MON from the protocol, the Vault must make requests to each of the Validators to "[unbond](../../definitions.md)" (unstake) MON from network staking. These requests are enforced by the network and by the protocol, but they don't happen immediately.&#x20;

