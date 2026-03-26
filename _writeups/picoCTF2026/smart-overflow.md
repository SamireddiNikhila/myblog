---
layout: default
title: "picoCTF 2026: smart_overflow"
level: "Beginner"
description: "Exploiting integer overflow in a Solidity contract to manipulate account balances."
category: "picoCTF-BC"
---

## Description
This challenge features a simple token contract where users can buy and sell tokens. The goal is to obtain a balance much higher than the total supply.

## Analysis
The vulnerability exists in the `transfer` function where it checks the user's balance but fails to account for **Integer Overflow**. In older Solidity versions (pre-0.8.0), adding to a variable at its maximum value ($2^{256} - 1$) would cause it to wrap back around to 0.



## Solution
By sending a value that, when added to the current balance, exceeds the `uint256` limit, we can "reset" the balance or bypass requirement checks.

### Exploit:
If we have a balance of 1 and we subtract 2, the balance doesn't become -1 (since it is unsigned); it wraps around to a massive number.

```bash
# Using cast to trigger the overflow
cast send $CONTRACT "transfer(address,uint256)" $ATTACKER_ADDRESS $LARGE_VALUE --private-key $PRIVATE_KEY