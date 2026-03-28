---
layout: default
title: "picoCTF 2026: hashcrack"
level: "Beginner"
description: "Performing a wordlist-based dictionary attack to recover a plaintext password from its SHA-256 hash."
category: "picoCTF-Crypto"
---

## Description
We found a leaked hash from an interstellar database. Can you find the original password to unlock the next sector?

## Analysis

rockyou.txt does a lot of heavy lifting in CTFs. dictionary attacks are underrated.

Cryptographic hashes are one-way, meaning they cannot be reversed through a simple formula. To find the plaintext, we must perform a **Dictionary Attack**. By hashing every word in a known list and comparing it to our target, we can identify the original password if it exists in our dictionary.



## Solution
I wrote a Python script to iterate through the common `rockyou.txt` wordlist, hashing each entry using the `hashlib` library until a match was found.

### Python Exploit Script:
```python
import hashlib

# The target hash provided by the challenge
target_hash = "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8"

def crack_hash():
    # Path to your wordlist
    wordlist_path = "/usr/share/wordlists/rockyou.txt"
    
    try:
        with open(wordlist_path, "r", encoding="latin-1") as f:
            for line in f:
                password = line.strip()
                # Hash the current word using SHA-256
                guess_hash = hashlib.sha256(password.encode()).hexdigest()
                
                if guess_hash == target_hash:
                    print(f"[+] Password found: {password}")
                    return
    except FileNotFoundError:
        print("[-] Wordlist not found. Please check the path.")

crack_hash()