---
layout: default
title: "picoCTF 2026: cluster rsa"
level: "Hard"
description: "Breaking multiple RSA public keys by identifying shared prime factors through Batch GCD analysis."
category: "picoCTF-Crypto"
---

## Description
The Interstellar communication hub uses a massive cluster of RSA keys to secure its traffic. However, we suspect their entropy source is flawed. Can you find a way into the system?

## Analysis
The security of RSA depends on the difficulty of factoring $n = p \times q$. However, if two different public keys $n_1$ and $n_2$ share a prime factor (e.g., $p$), that prime can be found easily using the Euclidean Algorithm: $p = \gcd(n_1, n_2)$. 

Once $p$ is recovered, we can find $q = n / p$, calculate the private exponent $d$, and decrypt the message.



## Solution
I used a Python script to iterate through the provided list of $n$ values. For each key, I checked its GCD against every other key in the cluster.

### Python Exploit Script:
```python
import math
from Crypto.PublicKey import RSA
from Crypto.Util.number import inverse, long_to_bytes

# List of modulus values (n) from the challenge
moduli = [n1, n2, n3, ...] 
target_n = ... # The specific n that encrypted our flag
target_e = 65537
ciphertext = ...

def batch_gcd():
    for i in range(len(moduli)):
        for j in range(i + 1, len(moduli)):
            n1 = moduli[i]
            n2 = moduli[j]
            p = math.gcd(n1, n2)
            
            # If gcd is not 1, we found a shared prime!
            if p > 1:
                print(f"Found shared prime p: {p}")
                q = target_n // p
                phi = (p - 1) * (q - 1)
                d = inverse(target_e, phi)
                
                # Decrypt the flag
                plaintext = pow(ciphertext, d, target_n)
                print(f"Flag: {long_to_bytes(plaintext)}")
                return

batch_gcd()