---
layout: writeup
title: "Privacy"
level: "Intermediate"
category: "Ethernaut"
description: "Unlock a heavily shielded contract by navigating complex storage slot packing and array indexing."
---

## Analysis

Like the Vault challenge, the goal is to find a `bytes32` key. However, this contract has many variables, meaning the key is buried deeper in the storage slots.

**The Storage Layout Rules:**
1. Each slot is 32 bytes ($256$ bits).
2. Variables are packed into the same slot if they fit.
3. Arrays always start at a **new slot**.



**Mapping the Slots for Privacy.sol:**
* **Slot 0:** `bool public locked` (1 byte)
* **Slot 1:** `uint256 public ID` (32 bytes)
* **Slot 2:** `uint8 flattening`, `uint8 denomination`, `uint16 awkwardness` (Total: 4 bytes - they all pack here)
* **Slot 3:** `bytes32[3] data` -> `data[0]`
* **Slot 4:** `bytes32[3] data` -> `data[1]`
* **Slot 5:** `bytes32[3] data` -> `data[2]`

The code requires `data[2]`, which is sitting in **Slot 5**.

---

## Solution

The contract requires a `bytes16` key, but our storage data is `bytes32`. We must read Slot 5 and truncate the value to the first 16 bytes (32 hex characters + the `0x` prefix).

**Step-by-Step:**

1.  **Query Slot 5:** Use web3 to pull the raw hex from the 6th storage slot (index 5).
2.  **Truncate:** Take the first 16 bytes of that 32-byte string.
3.  **Unlock:** Call the `unlock()` function with the truncated key.

**Console Commands:**

```javascript
// 1. Get the 32-byte hex string from Slot 5
const rawData = await web3.eth.getStorageAt(instance, 5);

// 2. Truncate to bytes16 (0x + 32 characters = 34 total length)
const key = rawData.slice(0, 34);

// 3. Unlock the contract
await contract.unlock(key);

// 4. Check status
await contract.locked(); // Result: false