---
icon: plug
metaLinks:
  alternates:
    - /broken/spaces/MdICYXISKGqWUQXbHV5x/pages/AYXu8hFwy6MScPBp9yKR
---

# 3rd Party Integration Guide

## Depositing and Redeeming sHYPE tokens

Interacting with the Kintsu Staked HYPE contract involves two primary actions: depositing native HYPE to receive sHYPE shares and redeeming sHYPE shares to receive HYPE back. This process incorporates staking/unstaking using a batch mechanism and a fee structure.

### Depositing HYPE

Users can deposit native HYPE into the [`StakedHype`](stakedhype-contract.md#overview) contract to obtain sHYPE shares, which represent their proportional ownership of the total pooled HYPE within the protocol. It is a one-step process which will immediately exchange HYPE for sHYPE tokens.

* **`deposit(address receiver)`**: When calling this function, the amount of HYPE to deposit is specified via `msg.value`. The `receiver` address will receive the newly minted sHYPE shares. A crucial requirement is that the deposited amount (`msg.value`) must be greater than or equal to `MINIMUM_DEPOSIT` (= 0.01 HYPE).

It triggers an internal `_updateFees()` call to ensure the protocol fees are accounted for before calculating the redemption ratio and minting shares. A `Deposit` event is emitted upon successful completion.

### Redeeming sHYPE

Redeeming sHYPE shares for native HYPE is a two-step process designed to manage the unbonding of staked assets from the underlying protocol. This process involves requesting an unlock, a waiting period, and then finally redeeming the HYPE.

#### **Requesting an Unlock (`requestUnlock(uint96 shares)`)**:

1. Users initiate the redemption process by calling `requestUnlock`, specifying the amount of sHYPE `shares` they wish to redeem.
2. The specified sHYPE shares are transferred from the user to the `StakedHype` contract.
3. The amount of HYPE to redeem (`assets`) is calculated based on the current redemption ratio post applying a nominal exit fee of `0.05%` (unless exempted).
4. The unlock request is added to the current batch.
5. The requested shares along with the spotValue are added to the `batchWithdrawRequests` mapping for the corresponding `batchId`.
6. Each user's unlock requests are tracked in the `userUnlockRequests` mapping, allowing a user to have multiple pending unlock requests.

{% hint style="warning" %}
**Important:** Shares requested for unlock are put in escrow and cannot be transferred or used for further staking.
{% endhint %}

#### **Redeeming Unlocked HYPE (`redeem(uint256 unlockIndex, address payable receiver)`)**:

1. Users can call this function to claim their unlocked HYPE after the `COOLDOWN_PERIOD` has passed since the `submissionTime` of the batch containing their unlock request.
2. The user specifies the `unlockIndex` corresponding to their specific unlock request within their `userUnlockRequests` array.\
   The function checks that the specified `unlockIndex` is valid and that the batch associated with the request has been submitted (`batch.submissionTime > 0`) and the cooldown period has elapsed (`block.timestamp > batch.submissionTime + COOLDOWN_PERIOD`).
3. If the contract's current balance is less than the amount to be redeemed, `sweep()` needs to be called first to transfer spot balance from HyperCore to the Vault.
4. The user's specific unlock request is deleted from their `userUnlockRequests` array.
5. The pre-determined (at the time of unlock request) HYPE is transferred to the specified `receiver` address.

#### Cancelling Unlock Requests (`cancelUnlockRequest(uint256 unlockIndex)`)

Users have a limited window to cancel an unlock request.

* A request can only be cancelled before its batch is send for execution.
* The user specifies the `unlockIndex` of the request they wish to cancel.
* The function verifies that the request exists and that the current batch ID matches the batch ID of the request.
* The shares from the cancelled request are removed from the corresponding `batchUnlockRequests`.
* The unlock request is deleted from the user's `userUnlockRequests` array.
* The sHYPE shares are transferred back to the user.

## Key Considerations

* **Minimum Deposit:** Deposits must meet the `MINIMUM_DEPOSIT (0.01 HYPE)` requirement.
* **Redemption is Two-Step:** Unlike some staking mechanisms, redemption is not instant. It requires requesting an unlock and then waiting for a cooldown period after the batch is processed.
* **Batching:** Deposit and Unlock requests are processed in batches. The timing of when a batch is sent for execution depends on external calls to the `submitBatch()` function.
* **Cooldown Period:** After a batch is sent, there is a `COOLDOWN_PERIOD` before the redeemed HYPE can be claimed (NO cooldown period for a batch when its net outflow is zero).
* **Cancellation Window:** Unlock requests can only be cancelled before its batch is send for execution. The current batch can be found via the `currentBatchId` function.

{% hint style="success" %}
Make sure to check out the `StakedHype` [contract interface](contract-interface-abi-and-functions.md#condensed-stakedhype-contract-interface) for more details on how to interact with it.
{% endhint %}

