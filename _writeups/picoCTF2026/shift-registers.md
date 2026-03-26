---
layout: default
title: "picoCTF 2026: shift registers"
level: "Intermediate"
description: "Reversing a Linear Feedback Shift Register (LFSR) to recover the initial state and decrypt the bitstream."
category: "picoCTF-Crypto"
---

## Description
The transmission is encrypted using a simple hardware-based stream cipher. We managed to recover the feedback taps and the first few bytes of the output. Can you find the initial state?

## Analysis
This challenge uses an **LFSR (Linear Feedback Shift Register)**. Since the feedback mechanism is linear (using XOR), we can model the state transitions as a matrix multiplication over $GF(2)$. If we have the tap positions, we can simulate the register in reverse or solve for the initial seed.



## Solution
I implemented a Python script to simulate the LFSR. By brute-forcing the initial state (if the register length is small, like 16 or 24 bits) or using the **Berlekamp-Massey algorithm** for longer registers, we can recover the original seed.

### Python Exploit Script:
```python
def lfsr(state, taps):
    # Perform the XOR on the tap positions
    feedback = 0
    for tap in taps:
        feedback ^= (state >> tap) & 1
    
    # Shift the state and insert the feedback bit at the high end
    # Assuming a 16-bit register
    new_state = (state >> 1) | (feedback << 15)
    return new_state, state & 1

# Example taps and initial output found in challenge
taps = [15, 13, 12, 10]
output_needed = "10110..." 

def crack():
    # Brute force all possible 16-bit seeds
    for seed in range(0x10000):
        state = seed
        generated_bits = ""
        for _ in range(len(output_needed)):
            state, bit = lfsr(state, taps)
            generated_bits += str(bit)
        
        if generated_bits == output_needed:
            print(f"Found Seed: {hex(seed)}")
            return seed

crack()