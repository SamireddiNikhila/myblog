---
layout: writeup
title: "MagicNumber"
level: "Beginner"
category: "Ethernaut"
description: "providing the Ethernaut with a Solver(code size is 10 bytes at most), a contract that responds to whatIsTheMeaningOfLife() with the right 32 byte number.."
---

## Analysis

The `MagicNum` contract requires you to provide a solver contract that responds to `whatIsTheMeaningOfLife()` and returns the number **42**, encoded as a 32-byte value.


At first glance, this appears trivial. However, the challenge introduces a strict constraint: the solver contract’s **runtime bytecode must be at most 10 bytes**. This makes it impossible to use Solidity, as even the smallest compiled Solidity contract exceeds this limit.

Ethereum contracts are deployed in two phases:
1. **Creation bytecode** – executed once during deployment  
2. **Runtime bytecode** – stored on-chain and executed on every call  

The MagicNumber level validates the **runtime bytecode size**, not the creation code. Therefore, the solution must manually craft raw EVM bytecode.

---


## Solution

# 1 . Minimal Runtime Bytecode

The smallest possible EVM bytecode that returns `42` as a 32-byte value is:

602a60005260206000f3

This bytecode:
- Pushes `42` onto the stack
- Stores it in memory
- Returns exactly 32 bytes

The runtime size is **exactly 10 bytes**, satisfying the constraint.

---

# 2 . Creation Bytecode Wrapper

Since runtime bytecode must be returned during deployment, it is wrapped inside a small creation prefix.

600a600c600039600a6000f3602a60005260206000f3

## Foundry Scripts
<pre class="plain-code"><code>

# 1 .Deploy the solver contract
cast send  --rpc-url "$SEPOLIA_RPC_URL" --private-key "$PRIVATE_KEY" --gas-price 20gwei --create       0X600a600c600039600a6000f3602a60005260206000f3    
// returns SOLVER_ADDRESS 

# 2 . Set the solver in MagicNumber
cast send "$MAGICNUM_INSTANCE" "setSolver(address)" <SOLVER_ADDRESS> --private-key "$PRIVATE_KEY" --rpc-url "$SEPOLIA_RPC_URL"

</code></pre>