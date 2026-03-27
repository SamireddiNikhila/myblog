---
layout: default
title: "picoCTF 2026: Not TRUe"
level: "Hard"
description: "Breaking the NTRU lattice-based cryptosystem using the LLL lattice reduction algorithm."
category: "picoCTF-Crypto"
---

## Description
The scouts have upgraded to post-quantum encryption. They claim it's 'TRUe' security, but we suspect their implementation might be flawed.

## Analysis
This challenge implements the **NTRU** cryptosystem. NTRU's security is based on the hard problem of finding the shortest vector in a high-dimensional lattice. However, if the dimension $N$ is small or the coefficients are poorly chosen, we can use **Lattice Reduction** techniques like the **LLL Algorithm** to recover the private key.

## Solution
I used the SageMath environment (or a Python script with a lattice library) to construct the NTRU lattice. By applying LLL reduction to the basis matrix, the private key $f$ appears in the resulting reduced matrix.

### Python (SageMath) Exploit Script:
```python
# Parameters from the challenge
N = 167
p = 3
q = 128
h = [...] # The public key polynomial coefficients

# Construct the NTRU Lattice Matrix
# [ I   H ]
# [ 0  qI ]
def solve_ntru():
    # Create the matrix
    M = matrix.identity(2*N)
    for i in range(N):
        for j in range(N):
            M[i, N+j] = h[(j-i)%N]
        M[N+i, N+i] = q
    
    # Apply LLL Reduction
    M_reduced = M.LLL()
    
    # The first row often contains the private key f
    f_poly = M_reduced[0][:N]
    print(f"Recovered f: {f_poly}")
    # From here, use f to decrypt the ciphertext...

solve_ntru()