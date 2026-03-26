---
layout: default
title: "picoCTF 2026: Shared Secrets"
level: "Intermediate"
description: "Calculating a shared cryptographic secret using the Diffie-Hellman Key Exchange protocol."
category: "picoCTF-Crypto"
---

## Description
Two interstellar scouts are communicating using a shared secret. We've intercepted their public keys and the prime parameters. Can you recover the secret key they are using to encrypt their map?

## Analysis
This is a standard implementation of the **Diffie-Hellman (DH)** protocol. The security of DH relies on the difficulty of the **Discrete Logarithm Problem**. However, in this challenge, the prime $p$ was small enough (or the private key was weak enough) to allow for the recovery of the shared secret.



## Solution
I used a Python script to perform the modular exponentiation. Given Alice's public key $A$, Bob's public key $B$, and the prime $p$, the shared secret $S$ is $A^b \pmod p$.

```python
# Parameters from the challenge
p = 0xffffffffffffffff... # The large prime
g = 2 
A = ... # Alice's public key
B = ... # Bob's public key
b = ... # Bob's private key (recovered or provided)

# Calculate Shared Secret: S = A^b % p
shared_secret = pow(A, b, p)

print(f"Shared Secret: {shared_secret}")
# The secret is then usually hashed or converted to hex for the flag