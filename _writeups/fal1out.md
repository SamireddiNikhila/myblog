---
layout: writeup
title: "Fallout"
level: "Beginner"
category: "Ethernaut"
description: "Identify a critical typo in a contract's constructor that allows anyone to claim ownership."
---

## Analysis

In older versions of Solidity (pre-0.4.22), constructors were defined by creating a function with the **exact same name** as the contract.

In this challenge, the contract is named `Fallout`, but the "constructor" function is named `Fal1out` (with a '1' instead of an 'l'). Because the names do not match, Solidity treats `Fal1out()` as a **regular public function** that can be called by anyone at any time.

**The Flaw:**
> The `Fal1out()` function contains the logic `owner = msg.sender;`. Since it is public, any user can call it to hijack the contract.

---

## Solution

The exploit is extremely straightforward because the "constructor" is just a standard public function.

**Step-by-Step:**

1. **Call the Misspelled Function**
   Interact with the contract and call the `Fal1out()` function. Since it is payable, you can send 0 value or a small amount.
   
2. **Verify Ownership**
   Check the `owner()` variable to confirm your address is now the owner.

**Example Console Command:**

```javascript
// Call the misspelled 'constructor'
await contract.Fal1out({ value: toWei('0.0001') });

// Verify you are the owner
await contract.owner();