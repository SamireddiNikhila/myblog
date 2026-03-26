---
layout: default
title: "picoCTF 2026: interencdec"
level: "Beginner"
description: "A multi-layered decoding challenge involving Base64, ROT13, and string manipulation."
category: "picoCTF-Crypto"
---

## Description
We found a strange transmission that seems to be double-encoded. Can you peel back the layers to find the flag?

## Analysis
This challenge requires identifying common encoding schemes. The initial string features a character set consistent with **Base64**. After the first decoding step, the resulting text appears to be shifted using a **Caesar Cipher (ROT13)**.



## Solution
I used a Python script to automate the decoding process in one go. This avoids manual errors when moving data between different online tools.

### Python Exploit Script:
```python
import base64
import codecs

# The original encoded string from the challenge
encoded_str = "Y3Zwa1BGU3tmMXNoX2Z1dmN0X3FycDFxcl8yMDI2fQ=="

def solve():
    # Layer 1: Base64 Decode
    # We decode the bytes and then convert them back to a string
    layer1 = base64.b64decode(encoded_str).decode('utf-8')
    print(f"Layer 1 (Base64 Decoded): {layer1}")

    # Layer 2: ROT13 Decode
    # 'codecs' library handles the shift easily
    final_flag = codecs.encode(layer1, 'rot_13')
    
    print(f"Final Flag: {final_flag}")

solve()