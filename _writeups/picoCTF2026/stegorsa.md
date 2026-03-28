---
layout: default
title: "picoCTF 2026: stegorsa"
level: "Intermediate"
description: "Extracting hidden RSA keys from image metadata to decrypt a secure transmission."
category: "picoCTF-Crypto"
---

## Description
An image was intercepted from the "Interstellar" fleet. Rumor has it the encryption keys are hidden within the image itself.

## Analysis

RSA key hidden in image LSBs. zsteg pulled it out in seconds. two-stage exploit was fun.

This challenge is a two-part exploit:
1. **LSB Steganography:** Data is hidden in the Least Significant Bits of the image pixels.
2. **RSA Decryption:** The hidden data contains the parameters ($p, q, e$) or a PEM-encoded private key needed to decrypt the flag.

## Solution
I used `zsteg` to analyze the image and found a hidden SSH/RSA private key in the `b1,rgb,lsb,xy` stream. Instead of using OpenSSL, I implemented the decryption using a Python script for better control over the process.

### 1. Extraction:
`zsteg -E "b1,rgb,lsb,xy" challenge.png > private.key`

### 2. Decryption (Python Script):
I used the `pycryptodome` library to import the recovered key and decrypt the ciphertext.

```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP

# Load the extracted private key
with open("private.key", "rb") as f:
    key_data = f.read()
    key = RSA.import_key(key_data)

# Initialize the cipher
cipher = PKCS1_OAEP.new(key)

# Read and decrypt the flag
with open("flag.enc", "rb") as f:
    ciphertext = f.read()
    decrypted_message = cipher.decrypt(ciphertext)

print(f"Flag: {decrypted_message.decode()}")