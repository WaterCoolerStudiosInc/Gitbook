---
description: Details on select functions, including previous versions when relevant.
icon: function
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/MdICYXISKGqWUQXbHV5x/the-raa-protocol/architecture-and-integration/contract-interface-abi-and-functions
---

# Contract Interface, ABI, & Functions

## Contract Addresses

{% content-ref url="../../official-contract-addresses.md" %}
[official-contract-addresses.md](../../official-contract-addresses.md)
{% endcontent-ref %}

{% hint style="warning" %}
Full ABIs can be found in our published npmjs package. These condensed interfaces contain the most important functions for integrating with the Kintsu core protocol.
{% endhint %}

## StakedMonad Condensed Interface

```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity 0.8.26;

interface StakedMonad {
    struct Node {
        uint64 id;
        uint96 weight;
        uint96 staked;
    }

    struct UnlockRequest {
        /// @dev Number of shares the user will receive if the request is cancelled
        uint96 shares;
        /// @dev Value of the underlying asset that will be received upon redemption
        uint96 spotValue;
        /// @dev When this batch is submitted, redemption is available after withdraw delay
        uint40 batchId;
        /// @dev Store the exitFee (expressed in basis points) used for processing
        uint16 exitFeeInBips;
    }

    /**
     * @notice View all unique node ids in their current order
     * @dev The order is not guaranteed to remain constant between calls
     * @dev The order is not guaranteed to correlate to a position in `getNodes()`
     */
    function getNodeIds() external view returns (uint256[] memory);

    /**
     * @notice View all nodes in their current order
     * @dev The order is not guaranteed to remain constant between calls
     */
    function getNodes() external view returns (Node[] memory);

    /// @notice View a node by its unique node id
    function viewNodeByNodeId(uint64 nodeId) external view returns (Node memory node);

    /**
     * @notice Converts MON into LST shares at the current block timestamp
     * @dev `totalShares()` accounts for both minted and mintable shares
     */
    function convertToShares(uint96 assets) external view returns (uint96 shares);

    /**
     * @notice Converts LST shares into MON at the current block timestamp
     * @notice Does not apply exit fee
     * @dev `totalShares()` accounts for both minted and mintable shares
     */
    function convertToAssets(uint96 shares) external view returns (uint96 assets);

    /**
     * @notice Shares that could exist at the current block timestamp
     * @notice Includes both minted and mintable shares
     */
    function totalShares() external view returns (uint96);

    function getAllUserUnlockRequests(address user) external view returns (UnlockRequest[] memory);

    function getExitFeeBips() external view returns (uint16);

    function getManagementFeeBips() external view returns (uint16);

    /// @notice Shares that could be minted by the protocol at the current block timestamp
    function getMintableProtocolShares() external view returns (uint96 shares);

    function calculateStakeDeltas(uint96 newTotalStaked) external view returns (
        uint96 totalExcess,
        uint96 totalDeficit,
        uint96[] memory nodeExcesses,
        uint96[] memory nodeDeficits
    );

    /**
     * @notice Deposit asset into the protocol and receive shares to represent your position
     * @notice Deposit amount must be specified by `msg.value`
     * @param minShares - Minimum quantity of shares that must be minted to prevent front running
     * @param receiver - Recipient of the minted shares
     * @return shares - Quantity of shares minted
     */
    function deposit(uint96 minShares, address receiver) external payable returns (uint96 shares);

    /*
     * @notice Step 1 of 2 in process of withdrawing assets
     * @notice Transfers `shares` to the vault contract
     * @notice Unlock is batched into current batch request
     * @notice Applies exit fee if present
     */
    function requestUnlock(uint96 shares, uint96 minSpotValue) external returns (uint96 spotValue);

    /**
     * @notice Allow user to cancel their unlock request
     * @notice Most users will have 1 concurrent unlock request (`unlockIndex` == 0) but advanced users may have more
     * @notice Must be done before the associated batch is submitted
     * @dev Order of unlock requests is not guaranteed between cancellations
     */
    function cancelUnlockRequest(uint256 unlockIndex) external;

    /**
     * @notice Step 2 of 2 in process of withdrawing assets
     * @notice Associated batch must have been submitted and the cooldown period elapsed
     * @dev Might need to first call `sweep()` to make funds available
     * @dev Deletes the caller's unlock request
     */
    function redeem(uint256 unlockIndex, address payable receiver) external returns (uint96 assets);

    /**
     * @notice Processes deposit and withdrawal requests and allocates MON according to Registry weights
     * @dev Might need to first call `sweep()`
     * @dev For a net ingress batch, the amount bonded may be less than the requested amount due to integer arithmetic.
     *      The remaining amount, or 'dust' is scheduled to be bonded in the next batch.
     * @dev For a net egress batch, the amount unbonded may be less than the requested amount due to integer arithmetic.
     *      The remaining amount, or 'dust' is scheduled to be unbonded in the next batch.
     *      In this scenario, `totalPooled` is temporarily increased by this dust amount to account for the assets
     *      that were requested to be unbonded but were not unbonded from nodes, ensuring they remain trackable
     *      in the pool for the next attempt.
     */
    function submitBatch() external;

    /**
     * @notice Withdraws MON from completed un-delegations
     * @notice Stores withdrawn funds in the contract to fund redemptions
     * @dev Required number of epochs must have passed since calling `undelegate`
     * @dev All withdrawals must be valid and complete or none will
     */
    function sweep(uint64[] memory nodeIds, uint8 maxWithdrawsPerNode) external;

    /**
     * @notice Withdraws MON from completed un-delegations of disabled nodes
     * @notice Adds withdrawn funds into the current batch to be redelegated
     * @dev Required number of epochs must have passed since calling `undelegate`
     * @dev All withdrawals must be valid and complete or none will
     */
    function sweepForced(uint64[] memory nodeIds) external;

    /**
     * @notice Claims all claimable rewards and adds them to the next batch
     * @dev Specifying an invalid node id will revert
     */
    function compound(uint64[] memory nodeIds) external;

    /**
     * @notice Allows external funds to be added without minting shares
     * @dev Restricts usage when total pooled is below a threshold to prevent share inflation attacks
     * @param benefactor - Address used for tracking who is responsible for the contribution
     */
    function contributeToPool(address benefactor) external payable;
}
```

## StakedMonad contract ABI

{% file src="../../../.gitbook/assets/StakedMonad.json" %}
