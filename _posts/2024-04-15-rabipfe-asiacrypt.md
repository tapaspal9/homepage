---
layout: post
title: "Registration Model for Functional Encryption: Linear Functions with Access Control"
date: 2024-04-15
description: How to evaluate linear functions on encrypted data — with optional attribute-based access control — and no trusted key authority. A four-attempt build-up to registered (AB-)IPFE from pairings.
tags: functional-encryption registration-based pairings
toc:
  sidebar: left
related_publications: true
related_posts: false
---

<div style="position:absolute; width:0; height:0; overflow:hidden;" aria-hidden="true">
$$
\renewcommand{\vec}[1]{\boldsymbol{#1}}
\newcommand{\ip}[2]{\langle #1, #2\rangle}
\newcommand{\vecalpha}{\boldsymbol{\alpha}}
\newcommand{\vecx}{\mathbf{x}}
\newcommand{\vecy}{\mathbf{y}}
\newcommand{\vecz}{\mathbf{z}}
\newcommand{\vecv}{\mathbf{v}}
\newcommand{\vecm}{\mathbf{m}}
\newcommand{\ZZ}{\mathbb{Z}}
\newcommand{\Zp}{\mathbb{Z}_p}
\newcommand{\secp}{\lambda}
\newcommand{\poly}{\mathsf{poly}}
\newcommand{\Gi}{\mathbb{G}_1}
\newcommand{\Gii}{\mathbb{G}_2}
\newcommand{\Gt}{\mathbb{G}_T}
\newcommand{\mpk}{\mathsf{mpk}}
\newcommand{\msk}{\mathsf{msk}}
\newcommand{\hsk}{\mathsf{hsk}}
\newcommand{\sk}{\mathsf{sk}}
\newcommand{\pk}{\mathsf{pk}}
\newcommand{\ct}{\mathsf{ct}}
\newcommand{\crs}{\mathsf{crs}}
\newcommand{\Att}{\mathsf{Att}}
\newcommand{\bM}{\mathbf{M}}
$$
</div>

<style>
.ripfe-fig{margin:1.6rem 0;text-align:center}
.ripfe-node{display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.55rem .9rem;margin:.25rem;font-weight:600;background:rgba(128,128,128,.06)}
.ripfe-node small{font-weight:400;opacity:.7}
.ripfe-node--accent{border-color:var(--global-theme-color,#0076df);color:var(--global-theme-color,#0076df)}
.ripfe-node--good{border-color:#2e9e5b;color:#2e9e5b}
.ripfe-node--warn{border-color:#cc7a00;color:#cc7a00}
.ripfe-node--bad{border-color:#cc3a2f;color:#cc3a2f}
.ripfe-node--dashed{border-style:dashed;border-color:var(--global-theme-color,#0076df)}
.ripfe-arrow{font-size:1.2rem;opacity:.55;line-height:1;margin:.1rem}
.ripfe-flowrow{display:flex;align-items:center;justify-content:center;gap:.5rem;flex-wrap:wrap;margin:1.2rem 0}
.ripfe-edgelabel{font-size:.78rem;opacity:.7;font-style:italic;padding:0 .15rem}
.ripfe-twocol{display:flex;gap:1.5rem;flex-wrap:wrap;justify-content:center;margin:1.4rem 0}
.ripfe-panel{flex:1 1 270px;max-width:380px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05);text-align:center}
.ripfe-panel h4{margin:.1rem 0 .7rem;font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8}
.ripfe-panel--good{border-color:rgba(46,158,91,.6)}
.ripfe-panel--warn{border-color:rgba(204,122,0,.55)}
.ripfe-callout{border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0}
.ripfe-attack{border-left:4px solid #cc3a2f;background:rgba(204,58,47,.08);padding:.7rem 1.05rem;border-radius:0 8px 8px 0;margin:1.2rem 0}
.ripfe-attack b{color:#cc3a2f}
.ripfe-ladder{display:flex;flex-direction:column;gap:.5rem;max-width:560px;margin:1.4rem auto}
.ripfe-rung{display:flex;align-items:center;gap:.7rem;border:1px solid rgba(128,128,128,.35);border-radius:9px;padding:.5rem .8rem;background:rgba(128,128,128,.05)}
.ripfe-badge{flex:0 0 auto;width:1.6rem;height:1.6rem;border-radius:50%;display:flex;align-items:center;justify-content:center;font-weight:700;color:#fff;font-size:.9rem}
.ripfe-badge--good{background:#2e9e5b}.ripfe-badge--warn{background:#cc7a00}.ripfe-badge--bad{background:#cc3a2f}
.ripfe-rung small{opacity:.75}
</style>

<div class="ripfe-callout" markdown="1">
Based on joint work with **Pratish Datta** and **Shota Yamada** — *Registered FE beyond Predicates: (Attribute-Based) Linear Functions and more* {% cite datta2024registered %}. The paper appeared at Asiacrypt {% cite datta2024registered %}. This post is an intuitive tour of the ideas; the paper carries the full constructions and proofs.
</div>

## Computing on Encrypted Data — Without the Authority

**Functional encryption (FE)** lets a key for a function $f$ turn a ciphertext of $x$ into exactly $f(x)$ — and nothing else. Its most practical workhorse is **inner-product FE (IPFE)**: messages and keys are vectors, and decryption reveals $\ip{\vecx}{\vecy}$. That single capability covers weighted means, polynomial evaluation, thresholds, biometric matching, and nearest-neighbour search — and it bootstraps richer classes like quadratic functions.

But classical FE carries a heavy assumption: a **central authority** holds a master secret key and mints every function key. That authority can decrypt everything — the **key-escrow problem**. The *registration model* removes it: users generate their own keys and register with a transparent **key curator** that holds no secret.

Here's the catch this paper tackles. Registration had only been achieved for **predicates** — access control that reveals data all-or-nothing. Actual *computation* on encrypted data in the registration model — inner products, and inner products with access control — was open.

<div class="ripfe-callout" markdown="1">
**Open problem.** Can we build registered FE for function classes *beyond predicates* — in particular, attribute-based linear function evaluation?
</div>

This work answers yes: the first registered IPFE and registered attribute-based IPFE (plus a general construction from indistinguishability obfuscation), all without a trusted authority.

<div class="ripfe-twocol">
  <div class="ripfe-panel ripfe-panel--warn" markdown="1">
  <h4>Standard IPFE — key escrow</h4>

  An **authority** holds $\msk$ and issues every key $\sk_{\vecy}$.

  <div class="ripfe-node ripfe-node--warn">Authority &middot; holds msk</div>
  <div class="ripfe-arrow">&darr;</div>
  <div class="ripfe-node">issues sk<sub>y</sub></div>

  *Can decrypt every ciphertext.*
  </div>
  <div class="ripfe-panel ripfe-panel--good" markdown="1">
  <h4>Registered IPFE — no escrow</h4>

  Users make their **own** keys; a secret-less curator aggregates.

  <div class="ripfe-node">register pk<sub>i</sub>, y<sub>i</sub></div>
  <div class="ripfe-arrow">&darr;</div>
  <div class="ripfe-node ripfe-node--good">Key curator &middot; no secret</div>

  *No single party can decrypt.*
  </div>
</div>

## The Registration Model

A one-time trusted setup samples a common reference string $\crs$. To join, a user samples its own $(\pk,\sk)$ and sends $\pk$ together with a function $f_\pk$ to the **key curator**. The curator's job is purely deterministic bookkeeping — it holds no secret, so *anyone* could play the role: it aggregates each $(\pk, f_\pk)$ into the master public key $\mpk$ and hands the user a short **helper decryption key** $\hsk$.

<div class="ripfe-fig">
  <div class="ripfe-flowrow">
    <div class="ripfe-node">user $i$<br><small>samples $(\pk_i,\sk_i)$, registers $\pk_i$ with $\vecy_i$</small></div>
    <div class="ripfe-arrow">&rarr;</div>
    <div class="ripfe-node ripfe-node--accent">Key curator<br><small>deterministic, no secret</small></div>
    <div class="ripfe-arrow">&rarr;</div>
    <div class="ripfe-node">$\mpk$ and helper keys $\hsk_i$</div>
  </div>
  <div class="ripfe-flowrow">
    <div class="ripfe-node">$\ct \leftarrow \mathsf{Enc}(\mpk, \vecx)$</div>
    <div class="ripfe-arrow">&rarr;</div>
    <div class="ripfe-node ripfe-node--dashed">user $i$: decrypt with $(\sk_i,\hsk_i)$</div>
    <div class="ripfe-arrow">&rarr;</div>
    <div class="ripfe-node ripfe-node--dashed">$\ip{\vecx}{\vecy_i}$</div>
  </div>
</div>

The bar for "efficient" is sharp: the secret keys, master public key, and helper keys must be **polylogarithmic** in the number of users $L$ — i.e. $\poly(\secp, \ell_f, \log L)$ — and each user needs only $O(\log L)$ helper-key updates over the system's lifetime. Encryption needs only $\mpk$ (not the big $\crs$), keeping it as fast as ordinary IPFE.

As is now standard, it suffices to build a **slotted** registered FE — where $L$ is fixed at setup and everyone registers in one shot — and then lift it to the dynamic version with a *power-of-two* transformation that costs only an $O(\log L)$ overhead.

## Starting Point: Plain IPFE — and the Escrow

The classic IPFE of Abdalla, Bourse, De Caro, and Pointcheval works over a prime-order group:

$$
\mpk=(g,\, g^{\vecalpha}),\qquad
\ct=(g^{s\vecalpha+\vecx},\, g^s),\qquad
\sk_{\vecy}=\ip{\vecalpha}{\vecy}.
$$

To decrypt, raise $g^{s\vecalpha+\vecx}$ to $\vecy$ to get $g^{s\ip{\vecalpha}{\vecy}+\ip{\vecx}{\vecy}}$, strip the mask $g^{s\ip{\vecalpha}{\vecy}}$ using $g^s$ and the key $\ip{\vecalpha}{\vecy}$, and read off $\ip{\vecx}{\vecy}$ by a small discrete log.

The escrow lives in one place: the secret key $\sk_{\vecy}=\ip{\vecalpha}{\vecy}$ needs the **master secret** $\vecalpha$. In the registration model, the user must somehow get the benefit of that key *without* anyone holding $\vecalpha$. The fix unfolds over four attempts.

<div class="ripfe-ladder">
  <div class="ripfe-rung"><span class="ripfe-badge ripfe-badge--good">1</span><span><b>One user</b> &mdash; <small>put the vector into the master public key. Works.</small></span></div>
  <div class="ripfe-rung"><span class="ripfe-badge ripfe-badge--bad">2</span><span><b>Many users, naively</b> &mdash; <small>aggregate the keys. Collusion breaks it.</small></span></div>
  <div class="ripfe-rung"><span class="ripfe-badge ripfe-badge--warn">3</span><span><b>Bind &amp; pair</b> &mdash; <small>per-user randomness + pairings. Cross terms appear.</small></span></div>
  <div class="ripfe-rung"><span class="ripfe-badge ripfe-badge--good">4</span><span><b>Cancel cross terms</b> &mdash; <small>users publish helper components at registration. Done.</small></span></div>
</div>

## Building Registered IPFE in Four Attempts

### Attempt 1 — one user

The decryptor doesn't actually need $\sk_{\vecy}=\ip{\vecalpha}{\vecy}$; it only needs the **mask** $g^{s\ip{\vecalpha}{\vecy}}$. And in the registration model the master public key is *allowed to depend on* the registered vector $\vecy$. So let the user pick $r$, publish $\pk=g^r$, keep $\sk=r$, and have aggregation produce

$$
\mpk=\bigl(g,\, g^{\vecalpha},\, W=g^{\,r+\ip{\vecalpha}{\vecy}}\bigr),\qquad
\ct=\bigl(g^{s\vecalpha+\vecx},\, g^s,\, W^s=g^{\,sr+s\ip{\vecalpha}{\vecy}}\bigr).
$$

The user computes $g^{sr}$ from $g^s$ and $r$, divides it out of $W^s$, and recovers the mask $g^{s\ip{\vecalpha}{\vecy}}$. No master secret needed. For a single user, done.

### Attempt 2 — many users, naively

The obvious extension aggregates everyone into one $W$:

$$
W=\prod_{i} g^{\,r_i+\ip{\vecalpha}{\vecy_i}} = g^{\sum_i r_i + \sum_i\ip{\vecalpha}{\vecy_i}}.
$$

This is **broken**. The vectors aren't tied to their owners, so honest secret keys can be re-pointed:

<div class="ripfe-attack" markdown="1">
**Collusion attack.** Users 1 and 2, holding $\sk_1=r_1$ and $\sk_2=r_2$, can treat them as valid keys for $\vecy_1+\vecz$ and $\vecy_2-\vecz$ for *any* $\vecz$, because

$$
W = g^{\,r_1+\ip{\vecalpha}{\vecy_1+\vecz}}\cdot g^{\,r_2+\ip{\vecalpha}{\vecy_2-\vecz}}\cdot \prod_{i\neq 1,2} g^{\,r_i+\ip{\vecalpha}{\vecy_i}}.
$$

Together they learn $\ip{\vecx}{\vecy_1+\vecz}$ and $\ip{\vecx}{\vecy_1-\vecz}$ for arbitrary $\vecz$ — far more than authorized. The root cause: **each $\vecy_i$ is not bound to its index $i$.**
</div>

### Attempt 3 — bind the vectors, then pair

Fix the binding with per-user randomness $\beta_i$ baked into the $\crs$ (as $g^{\beta_i}$, $g^{\beta_i\vecalpha}$, $g^{1/\beta_i}$). Now $\pk_i=g^{\beta_i r_i}$ and

$$
W=\prod_{i} g^{\,\beta_i(r_i+\ip{\vecalpha}{\vecy_i})},\qquad
\ct=\bigl(g_T^{s},\, g_T^{s\vecalpha+\vecx},\, W^s\bigr),\quad g_T=e(g,g).
$$

Each user's thread is now separated by its own $\beta_i$ — but the price is that unmasking needs a **pairing**. User $i$ pairs $W^s$ with $g^{1/\beta_i}$:

$$
e(W^s,\, g^{1/\beta_i})
= g_T^{\,sr_i}\cdot g_T^{\,s\ip{\vecalpha}{\vecy_i}}\cdot
\underbrace{\prod_{j\neq i} g_T^{\,s\beta_j r_j/\beta_i}\cdot \prod_{j\neq i} g_T^{\,s\beta_j\ip{\vecalpha}{\vecy_j}/\beta_i}}_{\textsf{cross terms}}.
$$

The user strips $g_T^{sr_i}$ using $r_i$ — but the **cross terms** from all the *other* slots are still in the way. Removing them is the final hurdle.

### Attempt 4 — cancel the cross terms via registration

The elegant move: make each user *help* future decryptors cancel its contribution. The $\crs$ gains components $g^{1/\gamma}$ and $g^{\gamma\beta_j/\beta_k}$, and at registration user $i$ additionally publishes $\lbrace g^{\gamma\beta_i r_i/\beta_j}\rbrace_{j\neq i}$ inside its public key. Aggregation then folds these into **helper keys** that carry exactly the cross-term mass:

$$
\mpk=\Bigl(g_T,\, g_T^{\vecalpha},\, g_1^{1/\gamma},\, W=\textstyle\prod_{i} g_1^{\,\beta_i(r_i+\ip{\vecalpha}{\vecy_i})}\Bigr),\qquad
\hsk_i=\prod_{j\neq i}\bigl(g_2^{\,\beta_j\gamma r_j/\beta_i}\cdot g_2^{\,\beta_j\gamma\ip{\vecalpha}{\vecy_j}/\beta_i}\bigr),
$$

with ciphertext $\ct=(g_T^{s},\, g_T^{s\vecalpha+\vecx},\, g_1^{-s/\gamma},\, W^s)$. Now user $i$ can compute and remove the cross terms, leaving precisely the mask $g_T^{s\ip{\vecalpha}{\vecy_i}}$ — and out comes $\ip{\vecx}{\vecy_i}$. Both $\mpk$ and $\hsk_i$ stay compact, $\poly(\secp, n, \log L)$, exactly as a slotted registered FE demands. This **function-aggregation** technique — binding each registered vector to its slot and shipping the cancellation material in the public key — is the paper's core engine.

## Adding Access Control: Registered ABIPFE

Plain IPFE is powerful but fragile: every released key leaks a linear view of the data. Attribute-based IPFE (ABIPFE) gates each inner product behind an access policy. The paper builds a *registered* ABIPFE by fusing two aggregations:

<div class="ripfe-fig">
  <div class="ripfe-flowrow">
    <div class="ripfe-node">attribute-aggregation<br><small>from registered ABE ([Hohenberger et al. EC'23](https://ia.cr/2022/1500))</small></div>
    <div class="ripfe-arrow"> &plus; </div>
    <div class="ripfe-node ripfe-node--accent">function-aggregation<br><small>this work (registered IPFE)</small></div>
    <div class="ripfe-arrow">&rarr;</div>
    <div class="ripfe-node ripfe-node--good">registered ABIPFE</div>
  </div>
</div>

Each user now registers a vector $\vecy_i$ **and** an attribute set $\Att_i$; encryption happens under a policy $P=(\bM,\rho)$ expressed as a linear secret-sharing scheme (shares $\mathbf{u}=\bM\mathbf{v}$ of a secret $s$, reconstructable exactly when the attributes satisfy $P$). The trick is to randomize the IPFE component $W^s$ with a fresh $h\leftarrow\Gi$:

$$
\ct=\bigl(g_T^{s},\; g_T^{s\vecalpha+\vecx},\; g_1^{-s/\gamma},\; g_1^{-s/\pi},\; h^{s}W^{s},\; h^{\ip{\mathbf{v}}{\mathbf{m}_k}}\,T_{\rho(k)}^{s}\bigr).
$$

Decryption now produces an *extra* masking factor $e(h,g_2)^{s/\beta_i}$, which a user can cancel **only** if its attribute set $\Att_i$ satisfies the policy — using the same cross-term-cancellation idea, now applied to the attribute side. Unauthorized users, even holding valid IPFE keys, cannot strip the mask and learn nothing about $\ip{\vecx}{\vecy_i}$. The master public key and helper keys remain compact, $\poly(\secp,|\mathcal{U}_{\mathsf{att}}|, n, \log L)$.

## The Bigger Picture

Alongside the pairing-based schemes, the paper gives a **general** registered FE for arbitrary functions from indistinguishability obfuscation, which even supports an *exponential* number of users (its slotted $\crs$ grows only with $\log L$). The pairing-based constructions, like all known pairing-based registered ABE, support a bounded user count (their slotted $\crs$ is $O(L^2)$).

The takeaway: registration is no longer limited to access control. With function-aggregation — binding registered vectors to slots and distributing the cancellation material through public keys — we get **genuine computation on encrypted data**, optionally behind fine-grained policies, with **no trusted authority** and from standard pairings.

<div class="ripfe-callout" markdown="1">
Want the formal definitions, the slotted-to-full transformation, the obfuscation-based scheme, and the security proofs? They're in the paper {% cite datta2024registered %}.
</div>
