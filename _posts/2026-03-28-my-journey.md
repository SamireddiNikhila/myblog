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

i jumped straight into solidity. no roadmap, no courses — 
just the docs, failed transactions, and a growing list 
of things i didn't understand yet.

when i found Ethernaut, everything clicked in the worst 
possible way. i realized how many assumptions i'd been making. 
how many of them were wrong. that "it works" and "it's secure" 
are two completely different sentences.

---

## a year in — where i stand

- actively grinding Ethernaut — each level a new 
  attack vector burned into memory
- solving picoCTF 2026 blockchain + crypto challenges
- learning Rust and Solana's security model
- participating in audit contests and bug bounty 
  programs as **Nyra**

every challenge taught me something no tutorial could. 
GateKeeper One broke me for longer than i'd like to admit. 
PuzzleWallet made me untangle three chained bugs before 
i could even see the exploit path.

security isn't learned by reading about it. 
you learn it by failing at it — repeatedly — 
until the pattern burns itself into your brain.

---

## why this blog exists

because i wish it existed when i started.

every writeup here is a real vulnerability pattern. 
every solution is something i actually ran, broke, 
fixed, and ran again.

if you're somewhere at the beginning of this path — 
confused, stuck, wondering if you're doing it right — 
keep going. the confusion means you're learning.

this blog is my proof of work. 🔴

— Nyra