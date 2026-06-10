---
layout: page
title:  "Anamorphic Cryptography: Building Covert Channels in Modern Crypto Systems"
description:  continuing
img: assets/img/anam.jpg
importance: 8
category: work
related_publications: true
---

**Funding/hosting acknowledgements:** Topic Engineering Secure Systems of the Helmholtz Association (HGF) and supported by KASTEL Security Research Labs, Karlsruhe 

{% include figure.liquid loading="eager" path="assets/img/anam.jpg" class="img-fluid rounded z-depth-1" %}
<div class="caption">
Advancing cryptographic primitives that enable covert communication channels while remaining indistinguishable from ordinary secure communication. (Image generated using ChatGPT)
</div>

Modern cryptographic systems are designed to protect the confidentiality and authenticity of communication, but they typically reveal the existence of a secure channel. Anamorphic cryptography introduces a new paradigm where ordinary cryptographic objects can secretly carry additional hidden information, enabling covert communication without changing their external appearance.

This emerging area explores how public-key encryption and signature schemes can be extended to embed hidden messages while maintaining the security of conventional cryptographic operations. Such techniques have potential applications in privacy-preserving communication, censorship-resistant systems, and advanced secure infrastructures.

This project investigates the foundations of anamorphic cryptography by developing new constructions that overcome limitations of existing approaches. We introduce general anamorphic extensions based on practical public-key encryption schemes, enabling hidden messages to be embedded into standard ciphertexts while achieving strong security guarantees. Our work extends this framework to lattice-based encryption, identity-based encryption, and public-key signatures, showing that anamorphic capabilities can be achieved from widely used cryptographic primitives rather than relying on specialized assumptions.

These results contribute toward a broader vision of cryptographic systems that support both visible and hidden forms of secure communication. This is a running research project, and the related publications appeared at Crypto {% cite banerjee2026anamorphic %}.
