---
layout: page
title: Multi-Client Unbounded Attribute-Based Inner Product Functional Encryption
description: SNSF-funded project at the University of St. Gallen — PI Prof. Katerina Mitrokotsa. Completed.
img: assets/img/mc_abipfe.jpg
importance: 1
category: work
---

**Funding:** Swiss National Science Foundation (SNSF) &nbsp;·&nbsp; **Host:** University of St. Gallen &nbsp;·&nbsp; **PI:** Prof. Katerina Mitrokotsa &nbsp;·&nbsp; **Status:** Completed

{% include figure.liquid loading="eager" path="assets/img/mc_abipfe.jpg" class="img-fluid rounded z-depth-1" %}
<div class="caption">
Multi-client unbounded ABIPFE: each client encrypts its own data independently, at a length of its own choosing, while a function key evaluates a linear function across all clients' ciphertexts.
</div>

Attribute-based inner product functional encryption (ABIPFE) brings fine-grained, attribute-based access control to linear computations over encrypted data. Inner product functional encryption (IPFE) already underpins many practical tasks — computing averages and Hamming distances, and machine-learning settings such as federated learning — while keeping the underlying data encrypted. Layering attribute-based access control on top both limits what the encrypted data leaks and widens the primitive's use in sensitive domains such as healthcare, pharma, and banking.

The multi-client variant MC-ABIPFE, introduced by Nguyen, Phan, and Pointcheval (Asiacrypt'22), lets several clients encrypt independently under their own keys so that a linear function can be evaluated across all of their ciphertexts, with security preserved for the honest clients even when others are corrupted. Their construction, however, fixes both the per-client data length and the total number of clients at setup: ciphertexts grow with a worst-case bound regardless of the actual input size, and no new client can join once that bound is reached. This project removes both restrictions — formalizing and constructing an MC-ABIPFE scheme in which each client chooses its data length at encryption time (so ciphertext size scales with the real input) and an arbitrary, dynamically growing set of clients is supported — all from well-studied group-based assumptions.

**Publications:** Journal of Cryptology (JoC), IEEE EuroS&P, and PKC.
