---
layout: post
title: Computing on Unbounded Data with Attributes spanning Multiple Authorities
date: 2023-09-19
description: Evaluating linear functions on encrypted data when the attributes that gate access are issued by many independent authorities — with no preset bound on vectors, attributes, or authorities, in fast prime-order groups.
tags: functional-encryption multi-authority pairings unbounded
toc:
  sidebar: left
related_publications: true
related_posts: false
---

<div style="position:absolute; width:0; height:0; overflow:hidden;" aria-hidden="true">
$$
\newcommand{\ip}[2]{\langle #1, #2\rangle}
\newcommand{\bx}{\mathbf{x}}
\newcommand{\by}{\mathbf{y}}
\newcommand{\bu}{\mathbf{u}}
\newcommand{\bv}{\mathbf{v}}
\newcommand{\bz}{\mathbf{z}}
\newcommand{\bM}{\mathbf{M}}
\newcommand{\dbo}[1]{[\![#1]\!]_1}
\newcommand{\dbtw}[1]{[\![#1]\!]_2}
\newcommand{\dbt}[1]{[\![#1]\!]_T}
\newcommand{\concat}{\,\|\,}
\newcommand{\gid}{\mathsf{GID}}
\newcommand{\PK}{\mathsf{PK}}
\newcommand{\MSK}{\mathsf{MSK}}
\newcommand{\CT}{\mathsf{CT}}
\newcommand{\SK}{\mathsf{SK}}
\newcommand{\hash}{\mathsf{H}}
\newcommand{\dbdh}{\mathsf{DBDH}}
\newcommand{\smx}{s_{\max}}
$$
</div>

<div style="border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0" markdown="1">
Based on joint work with **Pratish Datta** — *Decentralized Multi-Authority Attribute-Based Inner-Product FE: Large Universe and Unbounded*, PKC 2023 {% cite datta2023decentralized %}. This post is an intuitive tour of the ideas; the paper carries the full constructions and proofs.
</div>

## One Computation, Many Gatekeepers

Suppose a company wants the **average salary of employees who hold both a driving license and a PhD** — computed over encrypted HR records. Or a ministry wants mental-health statistics across students of many departments and universities. Each of these is a *linear function on encrypted data* (an inner product), gated by an *access policy* over attributes. The twist: the attributes aren't owned by one party. A university issues "PhD," a transport agency issues "driving license," each department vouches for its own students.

**Attribute-based inner-product FE (AB-IPFE)** gives the computation-plus-access-control part: a key for a vector $\by$ and some attributes decrypts a ciphertext (carrying data $\bx$ under a policy $P$) to reveal $\ip{\bx}{\by}$ — only if the attributes satisfy $P$. But classical AB-IPFE assumes a **single authority** minting all keys. Real attributes live across **many independent authorities** that should never have to coordinate.

That decentralized setting is **multi-authority AB-IPFE (MA-ABIPFE)**: each authority runs its own setup and issues keys for *only* the attributes under its control, with no inter-authority communication. A user gathers keys from whichever authorities certify their attributes and, if those satisfy the policy, learns $\ip{\bx}{\by}$.

<div style="margin:1.6rem 0;text-align:center">
  <div style="display:flex;align-items:center;justify-content:center;gap:.5rem;flex-wrap:wrap;margin:1.1rem 0">
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">Authority&nbsp;1<br><small style="font-weight:400;opacity:.72">"PhD"</small></div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">Authority&nbsp;2<br><small style="font-weight:400;opacity:.72">"driving license"</small></div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">Authority&nbsp;3<br><small style="font-weight:400;opacity:.72">"dept. X"</small></div>
    <div style="font-size:.85rem;opacity:.6">&nbsp;&hellip;&nbsp;</div>
  </div>
  <div style="font-size:.78rem;opacity:.72;font-style:italic">each issues keys for its own attributes &mdash; no coordination</div>
  <div style="display:flex;align-items:center;justify-content:center;gap:.5rem;flex-wrap:wrap;margin:1rem 0">
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">User with identity $\gid$<br><small style="font-weight:400;opacity:.72">collects keys for vector $\by$</small></div>
    <div style="font-size:1.2rem;opacity:.55">&rarr;</div>
    <div style="display:inline-block;border:1.5px dashed var(--global-theme-color,#0076df);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">$\CT$: data $\bx$ under policy $P$</div>
    <div style="font-size:1.2rem;opacity:.55">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid #2e9e5b;color:#2e9e5b;border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">learns $\ip{\bx}{\by}$<br><small style="font-weight:400;opacity:.72">iff attributes satisfy $P$</small></div>
  </div>
</div>

The only prior MA-ABIPFE of [Agrawal, Goyal, Tomida, TCC 2021](https://ia.cr/2020/1266) (AGT) works — but at a steep price, and that price is the story of this paper.

## The Trouble with the State of the Art

<div style="display:flex;gap:1.5rem;flex-wrap:wrap;justify-content:center;margin:1.4rem 0">
  <div style="flex:1 1 250px;max-width:360px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05)" markdown="1">
  <div style="font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8;text-align:center;font-weight:700;margin-bottom:.5rem">Slow &amp; heavy</div>

  It lives in **composite-order** bilinear groups under **source-group** (subgroup-decision) assumptions. At the 128-bit level a single decryption takes around **five days** — impractical by any measure.
  </div>
  <div style="flex:1 1 250px;max-width:360px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05)" markdown="1">
  <div style="font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8;text-align:center;font-weight:700;margin-bottom:.5rem">Rigidly bounded</div>

  Vector lengths, the number of authorities, and the attributes per authority are all **fixed at setup**. New authorities or attributes can't join later, and ciphertext cost scales with the worst-case length bound — plus a "one-use" limit on how often an attribute appears in a policy.
  </div>
</div>

That sets up a sharp open problem.

<div style="border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0" markdown="1">
**Open problem.** Build an efficient MA-ABIPFE for expressive (LSSS) policies, free of the one-use restriction, in **prime-order** groups under a **weaker** assumption, where an **unbounded** number of authorities (each with unboundedly many attributes) can join *at any time* and **unbounded-length** data can be processed.
</div>

## Our Answer: MA-ABUIPFE

The paper resolves it by formulating and building **multi-authority attribute-based *unbounded* IPFE (MA-ABUIPFE)**, with three features the prior scheme lacked: (a) independent authorities control different attributes; (b) authorities join anytime, with no cap on how many can ever exist; (c) message and key vectors have no length fixed at setup — each authority generates its keys without committing to a vector length.

It comes in two flavours, and crucially it runs in **fast prime-order groups** under **target-group** assumptions (qualitatively weaker than source-group ones), while lifting the one-use restriction entirely.

<div style="display:flex;gap:1.5rem;flex-wrap:wrap;justify-content:center;margin:1.4rem 0">
  <div style="flex:1 1 250px;max-width:360px;border:1px solid rgba(46,158,91,.5);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05)" markdown="1">
  <div style="font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8;text-align:center;font-weight:700;margin-bottom:.5rem">Small-universe</div>

  Each authority controls a single (or bounded) attribute, but the number of authorities is **arbitrary**. Secure under the classic **DBDH** assumption — the same target-group assumption behind textbook ABE.
  </div>
  <div style="flex:1 1 250px;max-width:360px;border:1px solid rgba(46,158,91,.5);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05)" markdown="1">
  <div style="font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8;text-align:center;font-weight:700;margin-bottom:.5rem">Large-universe</div>

  Each authority controls **exponentially many** attributes, never enumerated at setup. Secure under a parameterized variant, **L-DBDH**, justified in the generic bilinear group model.
  </div>
</div>

## The Starting Point: Unbounded IPFE by Hash-and-Pair

How do you make vectors *unbounded*? The trick comes from the unbounded IPFE of Dufour-Sans and Pointcheval (DP), itself built on DBDH. Given a public key $\dbo{\alpha}$, encryption **amplifies entropy** by pairing it with a hash applied to each coordinate's *index*:

$$
\CT_{\bx}:\ C_0=\dbo{r},\quad \lbrace\, C_i=\dbt{x_i}\cdot e(\dbo{\alpha},\, r\,\dbtw{\hash(i)}) \,\rbrace_{i\in\mathcal{I}};
\qquad
\SK_{\by}:\ -\alpha\textstyle\prod_{j}\hash(j)^{y_j}.
$$

Because the hash $\hash$ stretches to *any* index on the fly, no length need be fixed in advance. When the index sets match, the key vector $\by$ pulls $\dbt{\ip{\bx}{\by}}$ out of $\prod_j C_j^{y_j}$ and a single pairing $e(C_0, \SK_{\by})$. The natural plan: graft this hash-and-pair idea onto a DBDH-based multi-authority ABE ([Datta–Komargodski–Waters](https://ia.cr/2021/1325)) to get an *unbounded* multi-authority scheme. It almost works — and where it breaks is the heart of the paper.

## The Obstacle: Encryption Doesn't Know Who Will Decrypt

In the multi-authority world, the per-user keys from different authorities are tied together by a hash of the user's global identity $\gid$ and key vector $\bu$ — something like 
$$\hash(\gid\concat\bu\concat j\concat k\concat\mathcal{I})$$. 

To extend an authority's key component to an unbounded length, the encryptor would need to evaluate that very hash. But it **can't**:

<div style="margin:1.3rem 0;text-align:center">
  <div style="display:flex;align-items:stretch;justify-content:center;gap:1.4rem;flex-wrap:wrap">
    <div style="flex:1 1 240px;max-width:320px;border:1.5px solid rgba(128,128,128,.45);border-radius:12px;padding:.8rem .9rem;background:rgba(128,128,128,.05)">
      <div style="font-size:.8rem;text-transform:uppercase;letter-spacing:.04em;opacity:.8;font-weight:700;margin-bottom:.4rem">at encryption, you know</div>
      <div>the data $\bx$ and the policy $P$<br><small style="opacity:.72">but <b>not</b> $\gid$ or $\bu$</small></div>
    </div>
    <div style="flex:1 1 240px;max-width:320px;border:1.5px solid rgba(128,128,128,.45);border-radius:12px;padding:.8rem .9rem;background:rgba(128,128,128,.05)">
      <div style="font-size:.8rem;text-transform:uppercase;letter-spacing:.04em;opacity:.8;font-weight:700;margin-bottom:.4rem">only at key time, you know</div>
      <div>the identity $\gid$ and key vector $\bu$<br><small style="opacity:.72">chosen long after the ciphertext exists</small></div>
    </div>
  </div>
  <div style="font-size:.78rem;opacity:.72;font-style:italic;margin-top:.5rem">the same ciphertext is decrypted by many users with different $\gid,\bu$ &mdash; so a hash on $\gid\concat\bu$ can't be precomputed at encryption</div>
</div>

A plain hash-and-pair simply can't let a data owner encrypt unbounded-length vectors here.

## The Key Idea: Hash-Decomposition

The fix is a **hash-decomposition** mechanism: split the one problematic hash into a product of two independent hashes, sorted by *who can compute what and when*.

$$
\hash(\gid\concat\bu\concat j\concat k\concat\mathcal{I}) \;=\; \hash_2(j\concat k\concat\mathcal{I}) \;\cdot\; \hash_3(\gid\concat\bu\concat j\concat k)
$$

with the first factor depending only on indices (so the **encryptor** can compute it) and the second depending on $\gid,\bu$ (so it waits for **decryption**):

<div style="margin:1.3rem 0;text-align:center">
  <div style="display:flex;align-items:center;justify-content:center;gap:.6rem;flex-wrap:wrap">
    <div style="display:inline-block;border:1.5px solid var(--global-theme-color,#0076df);color:var(--global-theme-color,#0076df);border-radius:10px;padding:.55rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">$\hash_2(j\concat k\concat\mathcal{I})$<br><small style="font-weight:400">computed at <b>encryption</b></small></div>
    <div style="font-size:1.1rem;opacity:.6">$\times$</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.55rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">$\hash_3(\gid\concat\bu\concat j\concat k)$<br><small style="font-weight:400;opacity:.72">computed at <b>decryption</b></small></div>
    <div style="font-size:1.2rem;opacity:.55">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid #2e9e5b;color:#2e9e5b;border-radius:10px;padding:.55rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">full hash reassembled<br><small style="font-weight:400">at decryption time</small></div>
  </div>
</div>

The $\hash_2$ half carries no $\gid$ or $\bu$, so the **encryptor** can use it to expand an authority's public-key component $\dbo{y_{t,j}}$ into a target-group vector, 

$$\dbt{y_{t,j,k}^{(2)}} = e(\dbo{y_{t,j}},\, \hash_2(j\concat k\concat\mathcal{I})),$$ 

baking the unbounded stretch into the ciphertext without ever seeing the user. The $\hash_3$ half, which *does* depend on $\gid,\bu$, is supplied at decryption. To stitch the two halves back together correctly, the ciphertext carries an extra layer of secret-sharing of zero (components $C_{4,i,j}$), so the pairing operation that a single-authority scheme would do in one shot is now **distributed between the encryption and decryption algorithms**:

$$
e\big(\hash(\gid\concat\bu\concat j),\, \ip{C_{3,i,j}}{\bu}\big)
\;\longrightarrow\;
\textstyle\prod_{k} C_{3,i,j,k}^{\,u_k}\cdot e\big(C_{4,i,j},\, \hash_3(\gid\concat\bu\concat j\concat k)^{u_k}\big).
$$

The left side is what a bounded multi-authority scheme computes in one pairing; the right side is the unbounded version, with the $\hash_2$-dependent work pre-baked into $C_{3,i,j,k}$ at encryption and only the $\hash_3$-dependent piece finished at decryption. That redistribution is exactly what lets a data owner encrypt vectors of unbounded length without knowing the decryptor. The large-universe upgrade then layers on the Rouselakis–Waters technique so attributes need never be enumerated at setup.

## How Much Better?

<div style="overflow-x:auto;margin:1rem 0">
<table style="border-collapse:collapse;font-size:.74rem;line-height:1.35;margin:0 auto">
  <thead>
    <tr>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:left"></th>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:center">Prior MA-ABIPFE (AGT)</th>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:center">This work (MA-ABUIPFE)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;font-weight:700">Group order</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center">composite</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center;color:#2e9e5b;font-weight:700">prime</td>
    </tr>
    <tr>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;font-weight:700">Assumption</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center">subgroup (source-group)</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center;color:#2e9e5b;font-weight:700">DBDH / L-DBDH (target-group)</td>
    </tr>
    <tr>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;font-weight:700">Vector length</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center">bounded</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center;color:#2e9e5b;font-weight:700">unbounded</td>
    </tr>
    <tr>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;font-weight:700">Authorities / attributes</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center">bounded at setup</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center;color:#2e9e5b;font-weight:700">unbounded, join anytime</td>
    </tr>
    <tr>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;font-weight:700">One-use restriction</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center">yes</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center;color:#2e9e5b;font-weight:700">removed</td>
    </tr>
    <tr>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;font-weight:700">Decryption (128-bit)</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center">~5 days</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center;color:#2e9e5b;font-weight:700">minutes</td>
    </tr>
    <tr>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;font-weight:700">Public-key size</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center">baseline</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .55rem;white-space:nowrap;text-align:center;color:#2e9e5b;font-weight:700">~99% smaller</td>
    </tr>
  </tbody>
</table>
</div>

## Why It Matters

By distributing one stubborn hash across encryption and decryption, hash-decomposition turns a five-day, rigidly bounded, composite-order scheme into a prime-order one that decrypts in minutes, shrinks public keys by around 99%, drops the one-use restriction, and lets authorities and attributes — and the data itself — grow without limit. And it does so under **DBDH**, a long-studied target-group assumption weaker than what the prior scheme required, demonstrating that a functionality well beyond "all-or-nothing" access control can rest on the same footing as textbook ABE.

A useful by-product falls out along the way: a **single-authority** attribute-based unbounded IPFE from DBDH — the first AB-IPFE of any kind based on a target-group assumption, broadening the set of assumptions this primitive can stand on.

<div style="border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0" markdown="1">
Want the full constructions, the large-universe upgrade, and the static-security proofs? They're in the paper {% cite datta2023decentralized %}.
</div>
