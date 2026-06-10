---
layout: page
title:  Beyond Trusted Authorities: Registered Functional Encryption and Access Control
description:  Advancing cryptographic systems beyond centralized trust through registered encryption paradigms (continuing)
img: assets/img/reg-ab-fe.jpg
importance: 1
category: work
related_publications: true
---

**Fnding/hosting acknowledgements:** Topic Engineering Secure Systems of the Helmholtz Association
(HGF) and supported by KASTEL Security Research Labs, Karlsruhe &nbsp;·&nbsp; NTT Social Informatics Laboratories Japan 
>
>
{% include figure.liquid loading="eager" path="assets/img/reg-ab-fe.jpg" class="img-fluid rounded z-depth-1" %}
<div class="caption">
Redefining trust in advanced encryption systems: Eliminating centralized key-issuing authorities through decentralized registration-based architectures.
</div>

Modern cryptographic systems increasingly need to support secure computation and fine-grained access control in environments where trust is distributed across many parties. Traditional functional encryption (FE) and attribute-based encryption (ABE) often rely on a central authority that generates and manages users’ secret keys. While powerful, this trust model creates practical challenges: a compromised authority can undermine security, and deploying such systems at scale requires managing sensitive key-generation infrastructure.

Registration-based cryptography provides a new paradigm that addresses these limitations. In registered FE and registered ABE, users generate their own key pairs and register their public information with a transparent curator or registration mechanism. This removes the need for a fully trusted key-issuing authority while preserving expressive encryption capabilities. Such designs are particularly relevant for modern applications involving cloud computing, decentralized infrastructures, privacy-preserving data sharing, and collaborative computation.

My research explores the foundations and constructions of registered FE/ABE systems, focusing on improving their expressiveness, efficiency, and security guarantees. The works investigate new approaches for building registered encryption systems from modern cryptographic assumptions, including lattice-based assumptions, and study how registration mechanisms can enable scalable support for complex access policies and computation.

Through these works, my goal is to advance practical and theoretically robust cryptographic frameworks where users can participate in secure computation ecosystems without relying on centralized trust.

The project is running and the results have appeared at Asiacrypt {% datta2024registered %}, {% pal2025framework %}, at PKC {% cite pal2026registered %}, and a pre-print {% pal2025registeredprf %}.

