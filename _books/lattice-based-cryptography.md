---
layout: book-page
title: Lattice-based Cryptography
author: Post-quantum foundations
cover: assets/img/library/lattices-crypto.jpg
categories: cryptography
tags: post-quantum
---

## What it is

Cryptography built on the apparent hardness of problems on point lattices — chiefly Learning With Errors (LWE) and Short Integer Solution (SIS), and their efficient ring variants. These assumptions support encryption, signatures, and advanced primitives, with security that can be reduced to worst-case lattice problems.

## Why it matters — security & society

Lattice problems are believed to resist quantum attacks, which is why they underpin the first NIST-standardized post-quantum schemes (ML-KEM/Kyber and ML-DSA/Dilithium). As "harvest-now, decrypt-later" collection makes today's encrypted traffic a future liability, migrating to lattice-based cryptography protects the long-term confidentiality of communications, health, and financial data. The same machinery also enables fully homomorphic encryption, making lattices a backbone of privacy-preserving computation.

## Books & surveys

- **[A Decade of Lattice Cryptography](https://web.eecs.umich.edu/~cpeikert/pubs/lattice-survey.pdf)** — Chris Peikert. The go-to survey: SIS/LWE, hardness, and applications. *(survey)*
- **[Basic Lattice Cryptography: Kyber and Dilithium](https://eprint.iacr.org/2024/1287)** — Vadim Lyubashevsky. A concise, concrete path into the standardized schemes. *(notes)*
- **[On lattices, learning with errors, …](https://arxiv.org/abs/2401.03703)** — Oded Regev. The foundational LWE paper (updated edition). *(paper)*

## Lecture notes & courses

- **[Lattices in Cryptography](https://github.com/cpeikert/LatticesInCryptography/)** — Chris Peikert (Michigan). Graduate course notes, updated across offerings. *(notes)*
