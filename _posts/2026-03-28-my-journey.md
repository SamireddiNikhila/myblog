---
layout: post
title: "my journey into web3 security so far"
date: 2026-03-28
categories: personal
---

i didn't start in security. i started in solidity — 
building contracts, learning the EVM, figuring out 
how everything connects. but somewhere along the way 
i realized building wasn't enough for me.

i wanted to break things.

i kept looking at contracts thinking — *what if this 
input is unexpected? what if these two lines are flipped? 
what if someone does exactly what the contract doesn't expect?*

that itch didn't go away. so i stopped ignoring it.

---

## where it started

i jumped straight into solidity. no roadmap — 
just the docs, failed transactions, and a growing list 
of things i didn't understand yet.

a friend suggested Ethernaut. 
that recommendation changed everything.

suddenly every assumption i'd made about 
"safe code" was wrong. i realized that 
"it works" and "it's secure" are two 
completely different sentences.

---

## a year in — where i stand

- actively grinding Ethernaut — each level a new 
  attack vector burned into memory
- solving picoCTF 2026 blockchain + crypto challenges
- learning Rust and Solana's security model
- participating in audit contests and bug bounty 
  programs as **Nyra**

security isn't learned by reading about it. 
you learn it by failing at it — repeatedly — 
until the pattern burns itself into your brain.

---

## why this blog exists

because i wish it existed when i started.

here you'll find:
- **CTF + Ethernaut writeups** — real exploits, real POCs
- **deep concept breakdowns** — delegatecall, reentrancy, 
  storage slots, and everything that breaks your mental model
- **protocol analysis** — security lens on real DeFi protocols
- **tool guides** — Foundry, Cast, audit workflows
- **audit + bug bounty journey** — what i'm learning in the field
- **expanded X threads** — the ones worth going deeper on

if you're somewhere at the beginning of this path — 
confused, stuck, wondering if you're doing it right — 
keep going. the confusion means you're learning.

this blog is my proof of work. 🔴

— Nyra