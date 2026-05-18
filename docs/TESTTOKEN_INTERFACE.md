# Smart Contract Interface Blueprint

This document outlines the core external and public functions, events, and state variables required to execute the zero-inflation, fixed-supply staking architecture.

---

## 1. Storage & Data Structures

To track individual investor stakes securely on-chain without trusting a centralized server.

```solidity
struct StakeInfo {
    uint256 principalAmount;   // Total tokens staked by the investor
    uint256 rewardAmount;      // Fully allocated reward calculated at initiation
    uint256 startTime;         // Block timestamp when staking started
    uint256 maturityTime;      // exact timestamp when the 45-day lock expires
    bool isActive;             // Status flag to track open stakes
}

// Mapping from investor wallet address to their staking profile
mapping(address => StakeInfo) public investorStakes;

// Global Pool Variables
uint256 public totalPoolSupply;    // Hard-capped total supply
uint256 public rewardPoolBalance;  // Active treasury funds allocated for rewards
