---
layout: default
title: "picoCTF 2026: access_control"
level: "Beginner"
description: "Bypassing weak ownership checks to call restricted administrative functions."
category: "picoCTF-BC"
---

## Description
The contract has a `claimFlag()` function that only the "owner" should be able to call. Can you become the owner?

## Analysis
The contract uses a simple `address public owner` variable. However, the initialization function or a "renounceOwnership" function is poorly implemented, allowing any user to overwrite the owner variable.



## Solution
I identified a function `init()` that was not protected by a constructor or an "initialized" boolean. Calling this allowed me to set my own address as the owner.

### Execution:
```bash
cast send $CONTRACT "init()" --private-key $PRIV --rpc-url $RPC
cast send $CONTRACT "claimFlag()" --private-key $PRIVATE_KEY --rpc-url $SEPOLIA_RPC_URL