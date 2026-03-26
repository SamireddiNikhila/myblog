---
layout: default
title: "picoCTF 2026: black cobra pepper"
level: "Intermediate"
description: "Brute-forcing a peppered hash by iterating through a wordlist with character injection."
category: "picoCTF-Crypto"
---

## Description
We've intercepted a hashed password from the 'Black Cobra' squad. They use a secret 'pepper' to spice up their security. Can you find the original password?

## Analysis
A **Pepper** is a secret constant added to a password before hashing. Unlike a salt, it is not stored in the database. However, if the pepper is short (e.g., a single alphanumeric character), it only adds a small amount of entropy. We can defeat this by appending every possible pepper character to every word in our dictionary.

## Solution
I implemented a Python script to automate the "pepper-injection" attack. I used the `hashlib` library to hash each combination of `word + pepper` until I found a match for the target hash.

### Python Exploit Script:
```python
import hashlib
import string

# Target hash from the challenge
target_hash = "7e7e...[redacted]...8f2a"
# Possible pepper characters (a-z, A-Z, 0-9)
peppers = string.ascii_letters + string.digits

def crack_pepper():
    # Using the standard rockyou wordlist
    with open("rockyou.txt", "r", encoding="latin-1") as f:
        for line in f:
            password = line.strip()
            for char in peppers:
                # Attempt: Password + Single Character Pepper
                attempt = password + char
                guess = hashlib.sha256(attempt.encode()).hexdigest()
                
                if guess == target_hash:
                    print(f"[+] Found! Password: {password}, Pepper: {char}")
                    print(f"Full Flag: picoCTF{{{attempt}}}")
                    return

crack_pepper()