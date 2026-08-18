---
icon: plug
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/MdICYXISKGqWUQXbHV5x/the-raa-protocol/architecture-and-integration/3rd-party-integration-guide
---

# 3rd Party Integration Guide

## Depositing and Redeeming sMON Tokens

Interacting with the Kintsu Staked Monad (sMON) contract involves two primary actions: depositing native MON to receive sMON shares and redeeming sMON shares to receive MON back. This process incorporates a staking mechanism, a fee structure, and a batched unbonding process for redemptions.

### Depositing MON

Users can deposit native MON into the [`StakedMonad`](stakedmonad-contract.md#staked-monad-contract-a.k.a.-the-vault) contract to obtain sMON shares, which represent their proportional ownership of the total pooled MON within the protocol. There are two functions available for depositing: `deposit` and `mint`. Both are one-step processes which will immediately exchange MON for sMON tokens.

* **`deposit(uint96, address receiver)`**: This is the standard deposit function adhering to the ERC7535/ERC4626 standard. When calling this function, the amount of MON to deposit is specified via `msg.value`. The `assets` parameter is present for standard compliance but is not directly used for the deposit amount (which comes from `msg.value`). The `receiver` address will receive the newly minted sMON shares. A crucial requirement is that the deposited amount (`msg.value`) must be greater than or equal to `MINIMUM_DEPOSIT`.
* **`mint(uint96 shares, address receiver)`**: This function allows users to specify the _number of sMON shares_ they wish to receive. The required amount of MON to send with the transaction (`msg.value`) is calculated based on the desired `shares` and the current redemption ratio (`convertToAssets`). The user must send at least this calculated amount of MON. Any excess MON sent will be refunded to the caller so 3rd parties must handle this refund. Similar to `deposit`, the required MON amount must meet the `MINIMUM_DEPOSIT`.

Both deposit functions trigger an internal `_updateFees()` call to ensure the protocol fees are accounted for before calculating the redemption ratio and minting shares. A `Deposit` event is emitted upon successful completion.

### Redeeming sMON

Redeeming sMON shares for native MON is a two-step process designed to manage the unbonding of staked assets from the underlying protocol. This process involves requesting an unlock, a waiting period, and then finally redeeming the MON.

#### **Requesting an Unlock (`requestUnlock(uint96 shares)`)**:

1. Users initiate the redemption process by calling `requestUnlock`, specifying the amount of sMON `shares` they wish to redeem.
2. The specified sMON shares are transferred from the user to the StakedMonad contract.
3. The unlock request is added to a batch based on the current batch interval. The contract determines the `currentBatchUnlockId` based on the block timestamp and the `BATCH_INTERVAL_DELAY` from the `CREATION_TIME`.
4. The requested shares are added to the `batchUnlockRequests` mapping for the corresponding `batchId`.
5. Each user's unlock requests are tracked in the `userUnlockRequests` mapping, allowing a user to have multiple pending unlock requests.

{% hint style="warning" %}
**Important:** Shares requested for unlock are put in escrow and cannot be transferred or used for further staking.
{% endhint %}

#### **Sending Batch Unlock Requests (`sendBatchUnlockRequests(uint40[] calldata batchIds)`)**:

1. This function can be called by anyone, but is intended to be automated to process completed unlock request batches.
2. It takes an array of `batchIds` that are ready to be processed. These batch IDs must be in ascending order and must belong to past batch intervals (i.e., `batchId < currentBatchUnlockId`).
3. The function iterates through the provided batch IDs, calculates the total MON value (`aggregateSpotValue`) corresponding to the aggregated shares (`aggregateShares`) in those batches at the time of processing, based on the then-current redemption ratio.
4. The `batchUnlockRequests` for these batches are updated with the `spotValue` and `submissionTime` (the timestamp when the batch was sent for unbonding).
5. The total aggregated shares from the processed batches are burned from the contract's total supply.
6. The `_doUnbonding` function is called with the `aggregateSpotValue` to initiate the unbonding process with the underlying nodes, reducing the `totalPooled` amount.

{% hint style="info" %}
**Note:** This step continues the unbonding process but does not immediately return MON to the users.
{% endhint %}

#### **Redeeming Unlocked MON (`redeem(uint256 unlockIndex, address payable receiver)`)**:

1. Users can call this function to claim their unlocked MON after the `COOLDOWN_PERIOD` has passed since the `submissionTime` of the batch containing their unlock request.
2. The user specifies the `unlockIndex` corresponding to their specific unlock request within their `userUnlockRequests` array.\
   The function checks that the specified `unlockIndex` is valid and that the batch associated with the request has been submitted (`batch.submissionTime > 0`) and the cooldown period has elapsed (`block.timestamp > batch.submissionTime + COOLDOWN_PERIOD`).
3. The amount of MON to redeem (`assets`) is calculated proportionally based on the user's shares in the batch and the `spotValue` recorded for that batch when it was sent for unbonding.
4. If the contract's current balance is less than the amount to be redeemed, the `_doWithdrawUnbonded()` function is called to attempt to claim unbonded MON from the nodes.
5. The user's specific unlock request is deleted from their `userUnlockRequests` array.
6. The calculated `assets` (MON) are transferred to the specified `receiver` address.

#### Cancelling Unlock Requests (`cancelUnlockRequest(uint256 unlockIndex)`)

Users have a limited window to cancel an unlock request.

* A request can only be cancelled within the _same batch interval_ in which it was originally sent.
* The user specifies the `unlockIndex` of the request they wish to cancel.
* The function verifies that the request exists and that the current batch ID matches the batch ID of the request.
* The shares from the cancelled request are removed from the corresponding `batchUnlockRequests`.
* The unlock request is deleted from the user's `userUnlockRequests` array.
* The sMON shares are transferred back to the user.

## Key Considerations for Users

* **Minimum Deposit:** Deposits must meet the `MINIMUM_DEPOSIT` requirement.
* **Redemption is Two-Step:** Unlike some staking mechanisms, redemption is not instant. It requires requesting an unlock and then waiting for a cooldown period after the batch is processed.
* **Batching:** Unlock requests are processed in batches. The timing of when a batch is sent for unbonding depends on external calls to the `sendBatchUnlockRequests` function.
* **Cooldown Period:** After a batch is sent for unbonding, there is a `COOLDOWN_PERIOD` before the redeemed MON can be claimed.
* **Cancellation Window:** Unlock requests can only be cancelled within the same batch interval they were initiated. The active batch can be found via the `getBatchUnlockIdAtTime` function.
* **Slippage:** The value of sMON relative to MON can change between the time an unlock is requested (and the batch is sent) and the time it is redeemed, as the `spotValue` for the batch is fixed when the batch is sent.
* **Node Performance:** The ability to redeem MON after the cooldown period is dependent on the underlying staking nodes successfully unbonding and returning the MON to the contract. The `_doWithdrawUnbonded` function is called during redemption if necessary to pull unbonded funds.

{% hint style="success" %}
Make sure to check out the `StakedMonad` [contract interface](stakedmonad-contract.md#stakedmonad-contract-interface) for more details on how to interact with it.
{% endhint %}
