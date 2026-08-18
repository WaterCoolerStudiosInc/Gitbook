---
hidden: true
icon: globe
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/MdICYXISKGqWUQXbHV5x/the-raa-protocol/dao-and-governance
---

# DAO & Governance

## History & problem

We've achieved strong product-market fit for liquid staking. The market capitalization of leading liquid staking providers now represents a significant portion—approaching 10%—of total gas in TVL.

However, as Lido and Jito became dominant, concerns about centralization have risen. Traditional LSP DAO tokens lack control over stake delegation to validators, which poses risks of network centralization and compromised security. Instead, validator stake is delegated by a small centralized group of decision makers. Kintsu aims to solve this.

## Kintsu’s solution

Kintsu’s governance features an on-chain validator registry that is fully controlled by the DAO. This decentralization creates a marketplace for validators to compete for stake delegation by offering the best rewards profiles. This ensures that:

1. **Insiders cannot centralize stake** delegation to a small group of validators.
2. **Validators compete for stake delegation** by offering the best yield and building trust with the community.

## Validator Registry as a marketplace

There is no hand-picked favoritism on Kintsu. The Kintsu core protocol’s Liquid Staking Vault is an ERC-4626 style structure that delegates MON stake to each validator, targeting the overall weights reflected in Registry contract. Validator weights on the Registry are determined by DAO participants assigning their voting power to validators on the Registry.

Staking deposits and withdrawals are routed to validators in a way that makes the Vault's delegation percentages gradually align with the allocations set by the Registry, using Kintsu's Constant Rebalancing Algorithm. The system avoids automatically unstaking and rebalancing percentages whenever weights change, as this would reduce total staked assets below 100% TVL and lower staking APRs. Instead, the vault gradually achieves target percentages from the Validator Registry by allocating new deposits to understaked validators and removing stake from over-allocated validators during withdrawals.

This system allows all token stakers to control delegation weights in real-time, rather than relying on a multisig. As a result, Kintsu creates a marketplace where the DAO can select the most efficient node operators, turning the protocol into a true platform for validators.

## Voting power

Governance participants’ voting power is based on the number of KSU tokens they have. One token equals one vote. This voting power is used in two ways:

1. **To vote on governance proposals**
2. **To assign validator registry weights:** The amount of stake each validator receives is proportional to their share of the total voting power allocated on the Registry. These weights are “live”, so they are reflected on the Registry contract immediately after a DAO participant updates their preferences.

## Dual-token solution

### The KSU Token

KSU is the native governance token of Kintsu.

### The DAO NFT

The DAO NFT acts as a user’s ticket to all things governance on Kintsu. A user can acquire KSU tokens and stake them into governance. When they do this, a new DAO NFT is created and the KSU tokens are staked into it. Users can unstake their KSU tokens by burning the NFT at any time.

Governance participants (holders of a DAO NFT) can participate in governance activities including:

1. Voting on proposals
2. Assigning registry weights (controlling MON stake delegated to validators)
3. Making proposals
4. Earning yield on their staked KSU tokens

### KSU Staking Rewards

Users earn yield on their KSU tokens when they’re staked into a Kintsu DAO NFT.

## DAO Treasury

The DAO Treasury accumulates protocol management fees from staking rewards generated on Kintsu. The Treasury is owned by the governance contract on-chain. Governance can make proposals to use Treasury funds for things like grants, incentives, PoL, or any other reason they so choose, so long as the proposal passes a vote.

### Management fees

The Kintsu protocol charges a small Management Fee for providing liquid staking services. This fee takes the form of sMON inflation. Each time a user stakes or unstakes, the [Virtual Shares](../definitions.md) are updated on the `Vault`. These Virtual Shares are minted periodically, and go to the DAO's treasury.&#x20;

## Governance Council

Kintsu has a Governance Council that can act swiftly when needed. This council holds limited but crucial powers—including the ability to immediately disable validators on the Registry if they are found to be malicious actors.

## Governance Activities & Participation

{% content-ref url="governance-activities-and-participation.md" %}
[governance-activities-and-participation.md](governance-activities-and-participation.md)
{% endcontent-ref %}

