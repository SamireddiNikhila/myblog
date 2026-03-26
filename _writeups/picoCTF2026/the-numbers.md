---
layout: default
title: "picoCTF 2026: The Numbers"
level: "Beginner"
description: "Decoding a numeric substitution cipher by mapping integers to their corresponding alphabet positions."
category: "picoCTF-Crypto"
---

## Description
The message is just a sequence of numbers. Can you translate them back into the flag?

## Analysis
This is a basic **Substitution Cipher**. Each number corresponds to a letter of the alphabet based on its index (A=1, B=2, ..., Z=26). The structure of the numbers clearly mimics the `picoCTF{...}` format, with the numbers for 'P', 'I', 'C', and 'O' appearing at the start.

## Solution
I used a Python script to quickly map these integers back to characters. Using the `chr()` function with an offset of 64 allows us to convert the number `1` to 'A' (since the ASCII value of 'A' is 65).

### Python Exploit Script:
```python
# The sequence of numbers provided in the challenge
numbers = [16, 9, 3, 15, 3, 20, 6, 20, 8, 5, 14, 21, 13, 2, 5, 18, 19, 13, 1, 19, 15, 14]

def solve():
    flag = ""
    for num in numbers:
        # Convert number to character (1 -> 'A')
        # ASCII 'A' is 65, so 64 + 1 = 65
        char = chr(num + 64)
        flag += char
    
    # Manually add the picoCTF prefix format
    print(f"Decoded: PICOCTF{{{flag}}}")

solve()