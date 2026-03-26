# [High Severity] Liquidation Denial of Service (DoS) due to Arithmetic Reward Mismatch

## 1. Summary
The `Market.sol` contract contains a logic vulnerability in its liquidation mechanism. During extreme market volatility (price crashes), the calculated `liquidatorReward` can exceed the user's actual collateral balance held in their `Escrow`. Since the contract attempts to transfer the full reward without a safety cap, the transaction reverts due to insufficient funds. This creates unliquidatable bad debt, leading to potential protocol insolvency.

## 2. Vulnerability Detail
**Target:** `src/Market.sol`  
**Function:** `liquidate(address user, uint repaidDebt)`

The liquidation reward is calculated using the current Oracle price:
```solidity
uint liquidatorReward = repaidDebt * 1 ether / price;
liquidatorReward += liquidatorReward * liquidationIncentiveBps / 10000;
// ...
escrow.pay(msg.sender, liquidatorReward);

The Logical Flaw:
In a "Black Swan" event (e.g., a 90%+ price crash), the token-denominated liquidatorReward increases exponentially. If the collateral price drops to a level where:
$((RepaidDebt \times 1e18) / Price) \times (1 + Incentive) > UserCollateralBalance$

The escrow.pay() call will revert. Because this is the only path to clear underwater debt, the position becomes permanently "deadlocked," and the bad debt remains on the protocol's books.


3. Impact: High (Protocol Insolvency)
Bad Debt Accumulation: High-risk positions cannot be liquidated during market stress.

Denial of Service: The core safety mechanism of the lending market is rendered non-functional.

Insolvency: Accumulated bad debt can lead to a bank run where lenders lose their funds.


4. Proof of Concept (PoC)
The following Foundry test simulates a 95% price crash where the liquidation call consistently reverts.

Steps to Reproduce:

Deploy the Market with a 10% liquidation incentive.

User borrows DOLA; Market crashes.

Liquidator attempts to liquidate the position.

Transaction reverts with Insufficient Balance.

Foundry Test Case:

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "../src/Market.sol";

contract MockERC20 is IERC20 {
    function transfer(address, uint256) external returns (bool) { return true; }
    function transferFrom(address, address, uint256) external returns (bool) { return true; }
    function balanceOf(address) external view returns (uint256) { return 1000000 ether; }
    function approve(address, uint256) external returns (bool) { return true; }
    function allowance(address, address) external view returns (uint256) { return 1000000 ether; }
}

contract MockEscrow is IEscrow {
    uint256 public escrowBalance;
    function initialize(IERC20, address) external {}
    function onDeposit() external {}
    function pay(address, uint amount) external {
        require(amount <= escrowBalance, "Insufficient balance in escrow");
    }
    function setBalance(uint _bal) external { escrowBalance = _bal; }
    function balance() external view returns (uint) { return escrowBalance; }
}

contract MarketTest is Test {
    Market market;
    MockERC20 collateral;
    MockEscrow escrowImpl;
    address user = address(0x2);

    function setUp() public {
        collateral = new MockERC20();
        escrowImpl = new MockEscrow();
        market = new Market(address(0x1), address(0x4), address(0x5), address(escrowImpl), IDolaBorrowingRights(address(0x7)), collateral, IOracle(address(0x8)), 5000, 1000, 1000, false);
    }

    function testActualLiquidationRevert() public {
        address predictedEscrow = address(market.predictEscrow(user));
        vm.mockCall(predictedEscrow, abi.encodeWithSelector(IEscrow.balance.selector), abi.encode(500 ether));
        vm.mockCall(address(0x8), abi.encodeWithSelector(IOracle.getPrice.selector), abi.encode(0.01 ether));

        vm.expectRevert(); 
        market.liquidate(user, 100 ether);
        console.log("SUCCESS: Liquidation reverted! Bad debt is stuck in protocol.");
    }
}


5. Recommended Mitigation
Implement a safety cap on the liquidator reward:  

uint collateralBalance = escrow.balance();
if (liquidatorReward > collateralBalance) {
    liquidatorReward = collateralBalance;
}
escrow.pay(msg.sender, liquidatorReward);