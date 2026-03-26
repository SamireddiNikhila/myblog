---
layout: default
title: "picoCTF 2026: mod26"
level: "Beginner"
description: "Decrypting a ROT13 cipher using modular arithmetic principles."
category: "picoCTF-Crypto"
---

## Description
Cryptographic ciphers often involve modular arithmetic. Can you find the flag hidden behind this classic shift?

## Analysis
The challenge title "mod26" refers to the number of characters in the English alphabet. This is a classic **ROT13** (Rotate 13) cipher, a special case of the Caesar cipher where the alphabet is shifted by 13 positions. 

## Solution
I implemented a simple Python function to handle the character shifting. This approach ensures that non-alphabetical characters (like underscores and brackets) remain unchanged.

### Python Exploit Script:
```python
def rot13(s):
    result = ""
    for char in s:
        if 'a' <= char <= 'z':
            # Shift within lowercase range
            result += chr((ord(char) - ord('a') + 13) % 26 + ord('a'))
        elif 'A' <= char <= 'Z':
            # Shift within uppercase range
            result += chr((ord(char) - ord('A') + 13) % 26 + ord('A'))
        else:
            # Leave symbols and numbers alone
            result += char
    return result

ciphertext = "cvpbPGS{p0q3_fuvsg_f1zcy3_2026}"
print(f"Decoded Flag: {rot13(ciphertext)}")