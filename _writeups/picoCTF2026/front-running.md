---
layout: default
title: "picoCTF 2026: front_running"
level: "Intermediate"
description: "Exploiting the public nature of the mempool to steal a transaction's intended outcome."
category: "picoCTF-BC"
---

## Description
A secret password releases the flag. But even if you find the password, someone keeps beating you to the claim!

## Analysis

mempool visibility is a feature and a vulnerability at the same time. had to outbid the bot with priority fees.

On a public blockchain, all pending transactions are visible in the **Mempool**. If you send a transaction with the correct password, a bot can see your transaction, copy the password, and send a transaction with a higher **Gas Fee** to get processed first.



## Solution
To solve this, I had to use a higher `priority-fee` to ensure my transaction was included in the block before the bot's.

### Execution via Cast:
```bash

cast send $CONTRACT "solve(string)" "secret_password" \
--priority-gas-price 10gwei \
--private-key $PRIVATE_KEY