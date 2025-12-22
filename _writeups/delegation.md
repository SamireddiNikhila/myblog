---
layout: writeup
title: "Delegation"
level: "Intermediate"
category: "Ethernaut"
description: "Exploit the dangerous delegatecall function to hijack ownership of a proxy contract."
---

## Analysis

The `Delegation` contract acts as a proxy that forwards calls to a `Delegate` contract using `delegatecall`. 

**The Flaw:**
`delegatecall` is unique because it executes the code of the target contract but uses the **storage, state, and context** of the calling contract. This means if the `Delegate` contract changes a variable (like `owner`), it changes the variable in the `Delegation` (proxy) contract, not itself.



**Technical Trigger:**
In the `fallback()` function of the `Delegation` contract, any data sent to it is forwarded:
`address(delegate).delegatecall(msg.data);`

If we send the function signature for `pwn()`, the `Delegate` contract will execute its `pwn()` logic, but the `owner` variable inside the `Delegation` contract will be updated to our address.

---

## Solution

You can solve this entirely through the browser console by triggering the fallback function with the encoded function signature of `pwn()`.

**Step-by-Step:**

1.  **Generate the Function Signature:** You need the first 4 bytes of the Keccak-256 hash of the string `"pwn()"`.
2.  **Trigger the Fallback:** Send a transaction to the `Delegation` instance with that signature in the `data` field.
3.  **Verify:** Check the `owner` variable.

**Console Commands:**

```javascript
// 1. Get the function selector for pwn()
const pwnSignature = web3.utils.sha3("pwn()").slice(0, 10);

// 2. Send the transaction to trigger the fallback + delegatecall
await contract.sendTransaction({ data: pwnSignature });

// 3. Confirm you are the new owner
await contract.owner();