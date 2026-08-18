---
description: >-
  Details on select functions, along with the full StakedHype contract
  interface.
icon: function
metaLinks:
  alternates:
    - /broken/spaces/MdICYXISKGqWUQXbHV5x/pages/zRzNEIv1AZY2SPf8JHfg
---

# Contract Interface, ABI, & Functions

## Contract Address

{% content-ref url="../../official-contract-addresses.md" %}
[official-contract-addresses.md](../../official-contract-addresses.md)
{% endcontent-ref %}

## Condensed StakedHype Contract Interface

{% hint style="warning" %}
The full interface can be found in our published package. This condensed interface contains the most important functions for integrating with the Kintsu core protocol.
{% endhint %}

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.30;

interface IStakedHype {
    struct UnlockRequest {
        /// @dev Number of shares the user will receive if the request is cancelled
        uint96 shares;
        /// @dev Value of the underlying asset that will be received upon redemption
        uint96 spotValue;
        /// @dev When this batch is completed, redemption is available
        uint40 batchId;
        /// @dev Store the exitFee (expressed in basis points) used for processing
        uint16 exitFeeInBips;
    }

    /// @notice Converts HYPE into LST shares at the current block timestamp
    /// @dev `totalShares()` accounts for both minted and mintable shares
    function convertToShares(uint96 assets) external view returns (uint96 shares);

    /// @notice Converts LST shares into HYPE at the current block timestamp
    /// @dev `totalShares()` accounts for both minted and mintable shares
    function convertToAssets(uint96 shares) external view returns (uint96 assets);

    /// @notice Deposit HYPE into the protocol and receive LST shares to represent your position
    /// @notice HYPE must be greater than the minimum stake (= 0.01 HYPE)
    /// @dev Deposit amount must be specified by `msg.value`
    /// @param receiver - Recipient of the minted shares
    /// @return shares - Quantity of shares minted
    function deposit(address receiver) external payable returns (uint96 shares);

    /// @notice Step 1 of 2 in process of withdrawing HYPE
    /// @notice Transfers LST specified in `shares` argument to the vault contract
    /// @notice Unlock is batched into current batch request
    function requestUnlock(uint96 shares) external;

    /// @notice Allow user to cancel their unlock request
    /// @notice Most users will have 1 concurrent unlock request (`unlockIndex` == 0) but advanced users may have more
    /// @notice Must be done before the associated batch is submitted
    /// @dev Order of unlock requests is not guaranteed between cancellations
    function cancelUnlockRequest(uint256 unlockIndex) external;

    /// @notice Step 2 of 2 in process of withdrawing HYPE
    /// @notice Returns original deposit amount plus interest
    /// @notice Transfers HYPE to the caller
    /// @notice Associated batch must have been submitted and the cooldown period elapsed
    /// @dev Might need to first call `sweep()` if liquidity on the EVM side is low
    /// @dev Deletes the caller's unlock request
    function redeem(uint256 unlockIndex, address payable receiver) external returns (uint96 assets);
    
    /// @notice Processes deposit and withdrawal requests and allocates HYPE according to Registry weights
    /// @notice When deposits outweigh withdrawals, no delay is enforced
    /// @notice When withdrawals outweigh deposits, a delay from `cooldowns` in enforced
    function submitBatch() external;

    /// @notice Bridges all available HYPE from HyperCore:Spot to HyperEVM
    function sweep() external;
    
    /// @notice Syncs the HyperEvm storage state with HyperCore staking values
    /// @dev Cannot be called immediately after submitting a batch
    /// @dev Updates `totalPooled` and `Registry.node[].staked`
    function syncStaking() external;

    /// @notice Shares that could exist at the current block timestamp
    /// @notice Includes both minted and mintable shares
    function totalShares() external view returns (uint96 shares);

    /// @notice Shares that could be minted by the protocol at the current block timestamp
    function mintableProtocolShares() external view returns (uint96 shares);

    function getAllUserUnlockRequests(address user) external view returns (UnlockRequest[] memory);
}
```

## StakedHype Contract ABI

{% file src="../../../.gitbook/assets/StakedHype.json" %}
