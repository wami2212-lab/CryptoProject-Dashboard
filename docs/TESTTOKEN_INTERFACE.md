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



2. Core Operational Functions


stake

Allows an investor to lock up their tokens for the fixed 45-day duration.

Access Control: Public (Any Investor)

Logic:

Transfers principal from investor to the contract.

Calculates 100% of the guaranteed reward immediately.

Deducts the reward amount directly from the pre-funded Treasury pool to account for it upfront.

Sets maturityTime to block.timestamp + 45 days.
function stake(uint256 _amount) external;

claim
Allows investors to withdraw 100% of their principal and allocated rewards after the 45-day lock has successfully matured.

Access Control: Public (Any matured investor)

Logic:

Validates that block.timestamp >= maturityTime.

Transfers principalAmount + rewardAmount back to the investor's wallet.

Marks the stake as inactive.

function claim() external;


emergencyExit
The friction mechanism allowing early capital withdrawal at the cost of a penalty fee.

Access Control: Public (Active stakers before maturity)

Logic:

Validates that block.timestamp < maturityTime.

Forfeits 100% of the calculated pending rewards.

Calculates a 1% penalty fee on the investor's principalAmount.

Sends 99% of the principal back to the investor.

Automatically routes the 1% penalty back into the rewardPoolBalance (Penalty Redistribution).
function emergencyExit() external;


3. Global Ecosystem Events
Emitted to update the real-time investor dashboard instantly via Web3 event listeners.
event Staked(address indexed investor, uint256 principal, uint256 reward, uint256 maturityTime);
event Claimed(address indexed investor, uint256 totalWithdrawn);
event EmergencyWithdrawn(address indexed investor, uint256 returnedPrincipal, uint256 penaltyDeducted);


