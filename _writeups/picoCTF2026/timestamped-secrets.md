---
layout: default
title: "picoCTF 2026: timestamped secrets"
level: "Intermediate"
description: "Exploiting weak PRNG seeds by synchronized time-stamping to predict 'random' outputs."
category: "picoCTF-Crypto"
---

## Description
The system generates a 'unique' secret for every session, but it seems to rely on the current time to do so. If you can sync your clock with the server, the secret might not be so secret.

## Analysis

time.time() as an RNG seed. ±5 second brute force window was enough. clocks are not secrets.

The core vulnerability here is **Predictable Seeding**. Most Pseudo-Random Number Generators (PRNGs) are deterministic; if you know the `seed`, you can predict every subsequent number. By using `time.time()` as a seed, the "randomness" becomes a function of the clock.



## Solution
I captured the timestamp from the server's response headers. Since there might be a small delay (network latency), I wrote a Python script to test a small range of timestamps (±5 seconds) around the target time.

### Python Exploit Script:
```python
import random
import time

# The timestamp extracted from the server's 'Date' header
# Example: Thu, 26 Mar 2026 22:10:00 GMT -> 1774563000
target_time = 1774563000 

def crack_secret():
    # Loop through a small window to account for network lag
    for offset in range(-5, 6):
        test_seed = target_time + offset
        random.seed(test_seed)
        
        # Simulate the server's flag generation
        # (e.g., generating 16 random hex characters)
        potential_flag = "".join([hex(random.randint(0, 15))[2:] for _ in range(16)])
        print(f"Seed {test_seed}: picoCTF{{{potential_flag}}}")

crack_secret()