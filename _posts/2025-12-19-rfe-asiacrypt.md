---
layout: post
title: "Computing on Encrypted Data Without a Central Authority"
date: 2025-12-19
description: How registered functional encryption lets users learn a function of encrypted data with no trusted key authority — and a general framework that builds it from pairings via "user-specific pre-constraining."
tags: functional-encryption registration-based pairings
toc:
  sidebar: left
related_posts: false
related_publications: true
---

<div style="position:absolute; width:0; height:0; overflow:hidden;" aria-hidden="true">
$$
\renewcommand{\vec}[1]{\boldsymbol{#1}}
\newcommand{\Zp}{\mathbb{Z}_p}
\newcommand{\G}{\mathbb{G}}
\newcommand{\Gi}{\mathbb{G}_1}
\newcommand{\Gii}{\mathbb{G}_2}
\newcommand{\tr}{^\top}
\newcommand{\samp}{\leftarrow}
\newcommand{\otimesx}{\otimes}
\newcommand{\imp}[1]{[\![#1]\!]}
\newcommand{\impiii}[1]{[\![#1]\!]_{1,2}}
\newcommand{\bL}{\mathbf{L}}
\newcommand{\bT}{\mathbf{T}}
\newcommand{\bV}{\mathbf{V}}
\newcommand{\bU}{\mathbf{U}}
\newcommand{\bM}{\mathbf{M}}
\newcommand{\bI}{\mathbf{I}}
\newcommand{\bW}{\mathbf{W}}
\newcommand{\cF}{\mathcal{F}}
\newcommand{\out}{\mathsf{out}}
\newcommand{\pri}{\mathsf{pri}}
\newcommand{\pub}{\mathsf{pub}}
\newcommand{\mpk}{\mathsf{mpk}}
\newcommand{\msk}{\mathsf{msk}}
\newcommand{\hsk}{\mathsf{hsk}}
\newcommand{\sk}{\mathsf{sk}}
\newcommand{\pk}{\mathsf{pk}}
\newcommand{\ct}{\mathsf{ct}}
\newcommand{\crs}{\mathsf{crs}}
\newcommand{\Setup}{\mathsf{Setup}}
\newcommand{\Enc}{\mathsf{Enc}}
\newcommand{\Dec}{\mathsf{Dec}}
\newcommand{\Agg}{\mathsf{Agg}}
\newcommand{\sRFE}{\mathsf{sRFE}}
\newcommand{\MDDH}{\mathsf{MDDH}}
\newcommand{\pgb}{\mathsf{pgb}}
\newcommand{\br}{\mathbf{r}}
\newcommand{\bx}{\mathbf{x}}
\newcommand{\Garble}{\mathsf{Garble}}
\newcommand{\Eval}{\mathsf{Eval}}
\newcommand{\Sim}{\mathsf{Sim}}
\newcommand{\impi}[1]{[\![#1]\!]_1}
\newcommand{\impii}[1]{[\![#1]\!]_2}
\newcommand{\impt}[1]{[\![#1]\!]_t}
\newcommand{\ict}{\mathsf{ict}}
\newcommand{\isk}{\mathsf{isk}}
\newcommand{\Gt}{\mathbb{G}_t}
\newcommand{\rnd}{\mathsf{rnd}}
\newcommand{\att}{\mathsf{att}}
\newcommand{\zo}{\lbrace 0,1\rbrace}
$$
</div>

<style>
.rfe-fig{margin:1.6rem 0;text-align:center}
.rfe-node{display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.55rem .9rem;margin:.25rem;font-weight:600;background:rgba(128,128,128,.06)}
.rfe-node small{font-weight:400;opacity:.7}
.rfe-node--accent{border-color:var(--global-theme-color,#0076df);color:var(--global-theme-color,#0076df)}
.rfe-node--good{border-color:#2e9e5b;color:#2e9e5b}
.rfe-node--warn{border-color:#cc7a00;color:#cc7a00}
.rfe-node--dashed{border-style:dashed;border-color:var(--global-theme-color,#0076df)}
.rfe-arrow{font-size:1.2rem;opacity:.55;line-height:1;margin:.1rem}
.rfe-flowrow{display:flex;align-items:center;justify-content:center;gap:.5rem;flex-wrap:wrap;margin:1.2rem 0}
.rfe-edgelabel{font-size:.78rem;opacity:.7;font-style:italic;padding:0 .15rem}
.rfe-twocol{display:flex;gap:1.5rem;flex-wrap:wrap;justify-content:center;margin:1.4rem 0}
.rfe-panel{flex:1 1 270px;max-width:380px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05);text-align:center}
.rfe-panel h4{margin:.1rem 0 .7rem;font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8}
.rfe-panel--good{border-color:rgba(46,158,91,.6)}
.rfe-panel--warn{border-color:rgba(204,122,0,.55)}
.rfe-callout{border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0}
.rfe-chal{border-left:4px solid #cc7a00;background:rgba(204,122,0,.08);padding:.7rem 1.05rem;border-radius:0 8px 8px 0;margin:1rem 0}
.rfe-chal b{color:#cc7a00}
.rfe-branch{display:flex;flex-direction:column;gap:.5rem;align-items:flex-start}
.rfe-table{width:100%;border-collapse:collapse;margin:1rem 0;font-size:.86rem}
.rfe-table th,.rfe-table td{border:1px solid rgba(128,128,128,.35);padding:.4rem .6rem;text-align:center}
.rfe-table th{background:rgba(128,128,128,.12)}
.rfe-table .me{font-weight:700;background:rgba(128,128,128,.10)}
</style>

<div class="rfe-callout" markdown="1">
Based on joint work with **Robert Schädlich** — *A General Framework for Registered Functional Encryption via User-Specific Pre-Constraining*, ASIACRYPT 2025. Full version: [ia.cr/2025/2207](https://ia.cr/2025/2207). The paper appeared at Asiacrypt {% cite pal2025framework %}. This post is an intuitive tour of the ideas; the paper carries the constructions and proofs.
</div>

## Computation on Encrypted Data — But Who Holds the Keys?

**Functional encryption (FE)** is one of the most powerful generalizations of public-key encryption. Instead of all-or-nothing decryption, a holder of a function key for $f$ can take a ciphertext of $x$ and learn exactly $f(x)$ — and nothing more about $x$. That single idea captures access control, privacy-preserving analytics, and much of modern advanced cryptography.

There's a catch, though, and it's a big one: in standard FE a **central authority** holds a master secret key and hands out every function key. That authority can decrypt *everything*. This is the classic **key-escrow problem** — a single trusted party who must be trusted absolutely.

**Registered FE (RFE)** removes that party. Users generate their own keys and *register* their public keys with a transparent **key curator** that holds **no secret** at all. The curator's only job is bookkeeping: combine everyone's registered keys into one short master public key. No escrow, no trusted authority — yet decryption still yields exactly $f(x)$.

<div class="rfe-twocol">
  <div class="rfe-panel rfe-panel--warn" markdown="1">
  <h4>Standard FE — key escrow</h4>

  An **authority** holds $\msk$ and issues every function key.

  <div class="rfe-node rfe-node--warn">Authority &middot; holds msk</div>
  <div class="rfe-arrow">&darr;</div>
  <div class="rfe-node">issues sk<sub>f</sub> to each user</div>

  *The authority can decrypt everything.*
  </div>
  <div class="rfe-panel rfe-panel--good" markdown="1">
  <h4>Registered FE — no escrow</h4>

  Users make their **own** keys; a secret-less curator just aggregates.

  <div class="rfe-node">users register pk<sub>i</sub>, f<sub>i</sub></div>
  <div class="rfe-arrow">&darr;</div>
  <div class="rfe-node rfe-node--good">Key curator &middot; holds no secret</div>

  *No single party can decrypt.*
  </div>
</div>

Our work asks a sweeping question: **can we build registered FE for rich functionalities — fine-grained access control, Turing-machine policies, quadratic functions — from standard pairing assumptions, in a single unified framework?** The answer is yes, and the engine behind it is a notion we call *user-specific pre-constraining*.

## The Target: (Slotted) Registered Functional Encryption

Thanks to a "powers-of-two" compiler, it suffices to build the simpler **slotted RFE (sRFE)**, where the number of users $L$ is fixed and there's no dynamic update machinery to worry about. Here's the whole pipeline:

<div class="rfe-fig">
  <div class="rfe-flowrow">
    <div class="rfe-node">Each user $i$<br><small>generates $(\pk_i,\sk_i)$,<br>registers $\pk_i$ with function $f_i$</small></div>
    <div class="rfe-arrow">&rarr;</div>
    <div class="rfe-node rfe-node--accent">Key aggregator<br><small>transparent, no secrets</small></div>
    <div class="rfe-arrow">&rarr;</div>
    <div class="rfe-node">$\mpk$ and helper keys $\hsk_i$</div>
  </div>
  <div class="rfe-flowrow">
    <div class="rfe-node">$\ct \samp \Enc(\mpk, x)$</div>
    <div class="rfe-arrow">&rarr;</div>
    <div class="rfe-node rfe-node--dashed">user $i$: $\Dec(\sk_i, \hsk_i, \ct)$</div>
    <div class="rfe-arrow">&rarr;</div>
    <div class="rfe-node rfe-node--dashed">$f_i(x)$</div>
  </div>
</div>

Security demands that a user holding $\sk_i$ learns nothing about $x$ beyond $f_i(x)$. And there's a sharp efficiency bar — **compactness**: the master public key, helper keys, and ciphertexts must scale only *polylogarithmically* in the number of users $L$. You cannot, for instance, afford to stuff a description of every user's function into $\mpk$.

## The Classical Recipe — and Why It Breaks

In the *non-registered* world, a beautiful line of work builds expressive FE from a simple template:

> **inner-product FE (IPFE)** $+$ **linear garbling** $=$ FE for rich functions.

To see how, we first need the garbling gadget.

### The gadget: arithmetic key garbling (AKGS)

An **arithmetic key garbling scheme** (AKGS) is a lightweight, *one-time* secure way to encode a computation. For a function $f$ and two secrets $\sigma_0,\sigma_1$, it offers three algorithms:

<div class="rfe-callout" markdown="1">
- $\mathbf{L} = (\mathbf{L}_1,\dotsc,\mathbf{L}_m) \samp \Garble(f,\sigma_0,\sigma_1;\br)$ — using randomness $\br$, turn $f$ and the secrets into $m$ **affine label functions**, given by coefficient vectors $\mathbf{L}_j$. On a public input $\bx$ they produce labels $\vec\ell = (1,\bx)\cdot\mathbf{L}$, i.e. $\ell_j = (1,\bx)\cdot\mathbf{L}_j$. **Crucially, each $\mathbf{L}_j$ is linear in $\sigma_0,\sigma_1$ and $\br$.**
- $d \samp \Eval(f,\bx,\vec\ell)$ — a **linear** decoder that recovers $d = \sigma_1 f(\bx) + \sigma_0$.
- $\widetilde{\vec\ell} \samp \Sim(f,\bx,\, d)$ — a simulator that, given only the value $d$, outputs labels with the *same distribution* as the honest ones.
</div>

The simulator is what "one-time security" means: from the labels alone you learn nothing beyond $f(\bx)$ (encoded in $d$). Lin and Luo gave AKGS for arithmetic branching programs and for logspace Turing machines — which is exactly why the same framework reaches all of $\lbrace \mathsf{ABP}, \mathsf{L}, \mathsf{NL}\rbrace$.

### Lifting it with IPFE

A one-time gadget isn't enough on its own — reuse it twice and it leaks. The classical trick lifts it to full security using IPFE as a re-randomizer. Take key-policy ABE as the running example. To encrypt a message $\mu$ under attribute $\bx$, pick a fresh scalar $s$ and pack $(\mu, s(1,\bx))$ into an IPFE ciphertext; to make a key for policy $f$, garble it and pack the coefficient vectors into IPFE keys:

$$
\ct_{\bx}:\ \impi{(\mu,\, s,\, s\bx)}, \qquad
\sk_f:\ \impii{(1,\sigma_0,\vec 0)},\ \bigl\{\impii{(0,\mathbf{L}_j)}\bigr\}_{j\in[m]} .
$$

IPFE decryption pairs these up and reveals, **in the target group**, exactly the masked value and the randomized labels:

$$
\impt{\,\underbrace{\mu + s\,\sigma_0}_{d}\,}, \qquad
\bigl\{\,\impt{\,\underbrace{s\,(1,\bx)\cdot\mathbf{L}_j}_{\ell_j}\,}\,\bigr\}_{j\in[m]} .
$$

Now the linearity of $\Garble$ pays off: $s\mathbf{L} = \Garble(f, s\sigma_0, s\sigma_1; s\br)$, so running the linear $\Eval$ in the exponent yields $s\,\sigma_1 f(\bx) + s\,\sigma_0$. Following the convention that $f(\bx)=0$ means "authorized," the $\sigma_1$ term vanishes, the decryptor learns the mask $s\sigma_0$, subtracts it from $d$, and recovers $\mu$ (a discrete log in $\Gt$ for a polynomial-size message space).

Why is it secure? A suitable IPFE (function-hiding or slotted) guarantees that *only* the encodings of $d$ and the labels $\vec\ell$ ever leak. A DDH-style assumption then makes $(s\sigma_0, s\sigma_1, s\br)$ indistinguishable from **freshly sampled** randomness — so each decryption looks like an independent honest garbling, and the one-time security of the AKGS lifts to full IND-CPA.

### Why it breaks in the registration-based setting

This framework leans hard on two things: the ability to *sample garbling randomness while making a key*, and a *function-hiding* IPFE. Neither survives the move to registration. Porting it runs into three walls:

<div class="rfe-chal" markdown="1">
**Challenge 1 — randomness has nowhere to live.** In sRFE, secret keys are generated *independently of functions*; functions enter only during a **deterministic** aggregation step. But garbling labels need fresh randomness — and a deterministic step can't sample it.
</div>

<div class="rfe-chal" markdown="1">
**Challenge 2 — no function-hiding to lean on.** Classical proofs rely on *function-hiding* IPFE. In RFE the aggregation is transparent and deterministic by design (that's the whole point — an auditable registration). A meaningful function-hiding notion is hard to even define here, and adding one would betray the model.
</div>

<div class="rfe-chal" markdown="1">
**Challenge 3 — vectors must be group elements.** Pairing-based IPFE randomizes garbling by treating registered vectors as *group elements* (in $\Gi$ and $\Gii$) and invoking DDH-style assumptions. But existing sRFE-for-inner-products only register vectors over $\Zp$ — registering group elements seems out of reach.
</div>

## The Key Idea: Decompose the Garbling

The unlock is the very property the AKGS already gave us: its garbling is **linear in the randomness** (call it $\vec w$). That linearity lets a garbling split cleanly into two stages:

<div class="rfe-fig">
  <div class="rfe-flowrow">
    <div class="rfe-node rfe-node--accent">1. Sample random $\vec w \samp \Zp^{*}$<br><small>needs randomness, <b>not</b> the function</small></div>
    <div class="rfe-arrow">&rarr;</div>
    <div class="rfe-node">2. From the function, compute $\bL$<br>and output $\vec w\,\bL$<br><small>needs the function, fully <b>deterministic</b></small></div>
  </div>
  <div class="rfe-edgelabel">do step&nbsp;1 at <b>setup</b> (before functions are known) &middot; do step&nbsp;2 at <b>aggregation</b> (deterministic)</div>
</div>

This maps *exactly* onto the two registration phases: the randomized half happens at setup, where randomness is allowed; the deterministic half happens at aggregation, where the function is finally known. Challenge 1 dissolves.

But we need a building block that can compute an inner product **and** multiply by a matrix that was fixed earlier, at setup. That building block is the heart of the paper.

## The Engine: Inner Products with User-Specific Pre-Constraining

We introduce an sRFE for a functionality we call **pre-constrained inner product (Pre-IP)**. Compared to ordinary inner-product sRFE, the setup gets to bake in a *user-specific* matrix $\bT_i$ up front:

<div class="rfe-fig">
  <div class="rfe-flowrow">
    <div class="rfe-node">$\Setup$ fixes $\lbrace \bT_i \rbrace_i$<br><small>per-user pre-constraint</small></div>
    <div class="rfe-arrow">&rarr;</div>
    <div class="rfe-node">aggregator gets $(\pk_i, \bV_i)$</div>
    <div class="rfe-arrow">&rarr;</div>
    <div class="rfe-node">$\Enc$ produces a ciphertext for $\bU$</div>
  </div>
  <div class="rfe-flowrow">
    <div class="rfe-edgelabel">user $i$ decrypts to obtain</div>
    <div class="rfe-node rfe-node--accent">$\bU\,\bT_i\,\bV_i$</div>
  </div>
</div>

Now embed the garbling pieces into these matrices: the **randomness** $\vec w_i$ goes into the pre-constraint $\bT_i$ (chosen at setup), and the **deterministic** function matrix $\bL_i$ goes into $\bV_i$ (chosen at aggregation). Decryption multiplies them together and reconstructs the garbling. In the simplest case it really is this clean:

$$
\vec u\,\bT_i\,\bV_i \;=\; \bigl(\,\underbrace{\vec z-\underline{\vec w}_i}_{\vec p_{i,1}},\ \underbrace{\vec w_i\bL_i}_{\vec p_{i,2}}\,\bigr),
$$

which is exactly the garbling $(\vec p_{i,1}, \vec p_{i,2})$ of user $i$'s function — and applying the garbling's decoder gives $\vec z\,h_i(\vec x)\tr$.

Two more subtleties get handled by the same tool:

**One-time security made many-time.** A garbling is only one-time secure, so decrypting twice with one key could leak. We split the garbling randomness into a **slot-specific** part hidden in $\bT_i$ and a **ciphertext-specific** part carried by $\bU$ — so every ciphertext effectively gets a fresh garbling.

**Sidestepping function-hiding (Challenge 2).** Instead of function-hiding indistinguishability, we prove **simulation security**. The trick that makes it work: our simulator is given the decryption result in the *source groups* $\Gi$ and $\Gii$ — but **not** in $\Zp$.

**Group elements for free (Challenge 3).** Because the randomness now lives in $\bT_i$ (fixed at setup) rather than in the registered functions, we can evaluate the product $\impiii{\bU\,\bT_i}$ directly *in the groups* and randomize it via the **bilateral $\MDDH$** (i.e. bilateral $k$-Lin) assumption — so users never need to register vectors of group elements.

<div class="rfe-callout" markdown="1">
Our Pre-IP also allows **user-specific** pre-constraining — each user $i$ gets its own matrix $\bT_i$ — generalizing prior pre-constrained inner-product sRFE that forced a single fixed matrix across all users.
</div>

## Putting It Together: a Modular Framework

With Pre-IP in hand, the rest is modular. Combine Pre-IP with a garbling scheme for a function class $\cF \in \lbrace \mathsf{ABP}, \mathsf{L}, \mathsf{NL} \rbrace$ to get an sRFE for an intermediate functionality **Pre-1AWS$_\cF$**, parameterized by a pre-constraining matrix $\bM$:

$$
f(\bM, \vec x, \vec z) \;=\; \vec z\,\bM\,h(\vec x)\tr ,
$$

where $h$ is a vector of functions from $\cF$, $\vec x$ is the *public* input and $\vec z$ the *private* input. Then generic compilers lift this to the full target functionalities — and these compilers work for **any** $\cF$, so future garbling schemes plug right in.

<div class="rfe-fig">
  <div class="rfe-flowrow">
    <div class="rfe-node rfe-node--accent">Pre-IP</div>
    <div class="rfe-branch" style="align-items:center">
      <div class="rfe-edgelabel">+ garbling for $\cF$</div>
      <div class="rfe-arrow">&rarr;</div>
    </div>
    <div class="rfe-node">Pre-1AWS$_\cF$</div>
    <div class="rfe-arrow">&rarr;</div>
    <div class="rfe-branch">
      <div class="rfe-node rfe-node--dashed">AB-AWS$_\cF$</div>
      <div class="rfe-node rfe-node--dashed">AB-QF$_\cF$</div>
    </div>
  </div>
  <div class="rfe-edgelabel">$\cF \in \lbrace \mathsf{ABP},\ \mathsf{L},\ \mathsf{NL} \rbrace$ — arithmetic branching programs, deterministic / nondeterministic logspace Turing machines</div>
</div>

## What We Get

Instantiating the framework yields several firsts, all from the standard **(bilateral) $k$-Lin** assumption in asymmetric pairing groups:

- **Registered ABE for logspace Turing machines** ($\mathsf{L}$ and $\mathsf{NL}$) — the first such schemes. The public parameters scale only with the TM's number of *states*, independently of attribute length and the time/space bounds, so the system verifies **unbounded-length attributes**. This settles an open question of Zhu et al. (Asiacrypt 2023).
- **Registered FE for attribute-based quadratic functions (AB-QF)** with **compact** ciphertexts — the first quadratic RFE with built-in access control. A function $f=(g,h)$ on input $(\vec y,(\vec z_1,\vec z_2))$ returns $(\vec z_1\otimes\vec z_2)\,h\tr$ **iff** the policy $g(\vec y)=0$, with public attribute $\vec y$ and private $(\vec z_1,\vec z_2)$.

<table class="rfe-table">
  <thead>
    <tr><th>Work</th><th>Functionality</th><th>Security</th><th>Assumption</th></tr>
  </thead>
  <tbody>
    <tr><td>FFMMRV23 / DPY24</td><td>general</td><td>Adp-IND</td><td>iO + SSB</td></tr>
    <tr><td>DPY24</td><td>IP; AB-IP for LSSS</td><td>Adp-IND</td><td>GGM</td></tr>
    <tr><td>ZLZGQ24</td><td>IP; QF</td><td>Adp-IND / Sel-SIM</td><td>(bi-)$k$-Lin</td></tr>
    <tr><td>BLMMRW24</td><td>IP; weak QF</td><td>SelSta-IND / Adp-IND</td><td>$q$-type / GGM</td></tr>
    <tr><td>Pal–Schädlich 2025</td><td>AB-(1)AWS for ABP</td><td>Adp-IND / Sel*-SIM</td><td>bi-$k$-Lin</td></tr>
    <tr class="me"><td>This work</td><td>AB-AWS for ABP, L, NL</td><td>Sel*-SIM</td><td>bi-$k$-Lin</td></tr>
    <tr class="me"><td>This work</td><td>AB-QF for ABP, L, NL</td><td>Sel*-SIM</td><td>bi-$k$-Lin</td></tr>
  </tbody>
</table>

<p style="font-size:.82rem;opacity:.7;margin-top:-.3rem">IP = inner product, QF = quadratic functions, AWS = attribute-weighted sums; "Adp / Sel / SelSta / Sel*" = adaptive / selective / selective-with-static-corruptions / very-selective; "weak QF" = quadratic with fixed functions.</p>

## Why It Matters

The throughline is a single, reusable design: a generic compiler that fuses a **specialized inner-product RFE** (our Pre-IP) with **information-theoretic garbling**, transferring the classical Lin–Luo recipe into the registration-based world. Pre-IP cleanly encapsulates all the registration logic and hands the compiler exactly what it needs — fresh, user-specific garbling randomness sampled at setup and concealed until decryption.

The payoff is registered FE that is **expressive** (Turing-machine policies, quadratic functions), **access-controlled**, **compact**, and **simulation-secure** — all without a trusted key authority, and from standard pairing assumptions.

<div class="rfe-callout" markdown="1">
Want the full constructions, the compilers, and the security proofs? They're in the paper: [ia.cr/2025/2207](https://ia.cr/2025/2207).
</div>
