```
BTIP: 85
title: Support staking and penalize storage nodes that lose data
author: Shawn-Huang-Tron<shawn.huang@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/85
status: Draft
type: Core Protocol
category (*only required for Core Protocol):
created: 2025-03-27
```

## Simple Summary

This BTIP plan aims to improve data availability and enhance file data security in the BTFS network by introducing a staking mechanism and integrating it into the existing reputation scoring system.

## Abstract

Introducing a staking mechanism to BTFS to enhance network data reliability.

## Motivation

Currently, the penalties for miners in BTFS losing stored data are insufficient, which poses a barrier to user data protection. Therefore, it is proposed to add staking to protect user stored data. If a miner loses data, they will be penalized accordingly, and staking will be incorporated into the reputation score system.

## Specification

First, we need a smart contract to store miner-related staking information. This approach is more decentralized, fosters greater trust, and is a common industry practice for storing staking information.

Below are some core data structures of the contract:

```javascript
    // Global statistics
    uint256 public totalStaked;      // Total currently staked amount
    uint256 public totalUnlocked;    // Total unlocked pending withdrawal

    // Configuration parameters
    uint256 public unlockPeriod;     // Unlocking period in seconds
    uint256 public minStakeAmount;   // Minimum stake amount in wei

    // User stake information
    struct StakeInfo {
        uint256 stakedAmount;      // Currently active stake
        uint256 unlockTime;        // Timestamp when funds become withdrawable
        uint256 unlockedAmount;    // Amount available for withdrawal
    }
    
    mapping(address => StakeInfo) public stakers;

    // Events
    event Staked(address indexed user, uint256 amount);
    event Unstaked(address indexed user, uint256 amount, uint256 unlockTime);
    event Withdrawn(address indexed user, uint256 amount);
    event ParametersUpdated(uint256 newUnlock, uint256 newMin);
```

The core data structure lies in `stakers`, which records the staking information for each miner.

The following are the external methods exposed by the contract:

```javascript
    /**
     * @dev Stake BTT tokens into the contract
     */
    function stake() external payable {
        require(msg.value >= minStakeAmount, "Insufficient stake amount");
        
        StakeInfo storage info = stakers[msg.sender];
        info.stakedAmount += msg.value;
        totalStaked += msg.value;

        emit Staked(msg.sender, msg.value);
    }

    /**
     * @dev Initiate unstaking process
     * @param amount Amount to unstake in wei
     */
    function unstake(uint256 amount) external {
        // amount is wei
        amount *= 1e18;
        StakeInfo storage info = stakers[msg.sender];
        require(info.stakedAmount >= amount, "Exceeds staked amount");

        // Update global stats
        totalStaked -= amount;
        totalUnlocked += amount;

        // Update user state
        info.stakedAmount -= amount;
        info.unlockedAmount += amount;
        info.unlockTime = block.timestamp + unlockPeriod;

        emit Unstaked(msg.sender, amount, info.unlockTime);
    }

    /**
     * @dev Withdraw unlocked funds
     */
    function withdraw() external {
        StakeInfo storage info = stakers[msg.sender];
        require(block.timestamp >= info.unlockTime, "Funds still locked");
        require(info.unlockedAmount > 0, "No withdrawable balance");

        // Update global stats
        totalUnlocked -= info.unlockedAmount;
        uint256 amount = info.unlockedAmount;
        info.unlockedAmount = 0;

        payable(msg.sender).transfer(amount);
        emit Withdrawn(msg.sender, amount);
    }
```

Among them, the `stake` method is `payable`, allowing users to directly stake the corresponding BTT when calling this method. Secondly, if miners need to unstake, they can call the `unstake` method. However, to actually transfer the staked tokens, they ultimately need to call `withdraw`.

Furthermore, the contract will also expose some `view` methods, making it easier for miners to read some current state information of the contract:

```javascript
    // ================= View Functions =================
    
    /**
     * @dev Get user's stake information
     * @param user Address to query
     */
    function getUserStake(address user) external view returns (
        uint256 stakedAmount,
        uint256 unlockTime,
        uint256 unlockedAmount
    ) {
        StakeInfo memory info = stakers[user];
        return (info.stakedAmount, info.unlockTime, info.unlockedAmount);
    }

    /**
     * @dev Get global contract statistics
     */
    function getGlobalStats() external view returns (
        uint256 _totalStaked,
        uint256 _totalUnlocked,
        uint256 contractBalance
    ) {
        return (totalStaked, totalUnlocked, address(this).balance);
    }
```

Calling these two methods allows you to obtain miner staking information and network-wide staking statistics, respectively.

## Rationale

## Backwards Compatibility

This new feature is backward-compatible and won’t cause breaking changes.

## Test Cases

## Implementation
