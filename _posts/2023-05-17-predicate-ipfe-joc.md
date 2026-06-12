---
layout: post
title: Linear Computations with Predicates Hiding Attributes
date: 2023-05-17
description: Evaluating weighted statistics on encrypted medical records — gated by a hidden policy, with no preset bound on data or attribute size. The ideas behind unbounded, attribute-hiding predicate IPFE from pairings.
tags: functional-encryption attribute-based-encryption pairings unbounded
toc:
  sidebar: left
related_publications: true
related_posts: false
---

<div style="position:absolute; width:0; height:0; overflow:hidden;" aria-hidden="true">
$$
\newcommand{\ip}[2]{\langle #1, #2\rangle}
\newcommand{\bx}{\mathbf{x}}
\newcommand{\bw}{\mathbf{w}}
\newcommand{\by}{\mathbf{y}}
\newcommand{\bv}{\mathbf{v}}
\newcommand{\bz}{\mathbf{z}}
\newcommand{\bk}{\mathbf{k}}
\newcommand{\bc}{\mathbf{c}}
\newcommand{\bB}{\mathbf{B}}
\newcommand{\dbi}[1]{[\![#1]\!]_1}
\newcommand{\dbii}[1]{[\![#1]\!]_2}
\newcommand{\Rp}{\mathcal{R}_p}
\newcommand{\Rs}{\mathcal{R}_s}
\newcommand{\SK}{\mathsf{SK}}
\newcommand{\CT}{\mathsf{CT}}
\newcommand{\sxdh}{\mathsf{SXDH}}
$$
</div>

<div style="border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0" markdown="1">
Based on joint work with **Uddipana Dowerah, Subhranil Dutta, Aikaterini Mitrokotsa, and Sayantan Mukherjee** — *Unbounded Predicate Inner Product Functional Encryption from Pairings* {% cite dowerah2023unbounded %}, appeared in the Journal of Cryptology. This post is an intuitive tour of the ideas; the paper carries the full constructions and proofs.
</div>

## A Hospital, a Cloud, and a Statistic

Picture the Ministry of Health (MoH) wanting the average blood pressure of patients recently treated for influenza — computed over records that hospitals have **encrypted** and parked in a cloud. The MoH should learn that one statistic, and *only* for the patients matching a policy, without ever seeing the raw records or the sensitive identifiers attached to them.

This is exactly what **attribute-based inner-product FE (AB-IPFE)** promises. A hospital encrypts a patient's vitals $\bx$ (temperature, heart rate, blood pressure, …) under an **attribute** $\bw$ (SSN, age, sex, eligible designations, …). A research centre holds a key for a **policy** $P$ and a **weight vector** $\by$. Decryption reveals the weighted statistic $\ip{\bx}{\by}$ — but only if the attribute satisfies the policy.

<div style="margin:1.6rem 0;text-align:center">
  <div style="display:flex;align-items:center;justify-content:center;gap:.5rem;flex-wrap:wrap;margin:1.1rem 0">
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;margin:.25rem;font-weight:600;background:rgba(128,128,128,.06)">Hospital <small>(data owner)</small><br><small style="font-weight:400;opacity:.72">encrypts vitals $\bx$ under attribute $\bw$</small></div>
    <div style="font-size:1.2rem;opacity:.55;margin:.1rem">&rarr;</div>
    <div style="display:inline-block;border:1.5px dashed var(--global-theme-color,#0076df);border-radius:10px;padding:.5rem .85rem;margin:.25rem;font-weight:600;background:rgba(128,128,128,.06)">$\CT_{\bx,\bw}$ on the cloud<br><small style="font-weight:400;opacity:.72">attribute $\bw$ stays hidden</small></div>
  </div>
  <div style="display:flex;align-items:center;justify-content:center;gap:.5rem;flex-wrap:wrap;margin:1.1rem 0">
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;margin:.25rem;font-weight:600;background:rgba(128,128,128,.06)">MoH <small>(authority)</small><br><small style="font-weight:400;opacity:.72">issues $\SK_{P,\by}$</small></div>
    <div style="font-size:1.2rem;opacity:.55;margin:.1rem">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;margin:.25rem;font-weight:600;background:rgba(128,128,128,.06)">Specialist <small>(data user)</small></div>
    <div style="font-size:1.2rem;opacity:.55;margin:.1rem">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid #2e9e5b;color:#2e9e5b;border-radius:10px;padding:.5rem .85rem;margin:.25rem;font-weight:600;background:rgba(128,128,128,.06)">learns $\ip{\bx}{\by}$<br><small style="font-weight:400;opacity:.72">iff $P(\bw)$ holds</small></div>
  </div>
  <div style="margin-top:.4rem;line-height:1.9">
    <span style="display:inline-block;font-size:.72rem;border:1px solid #cc7a00;color:#cc7a00;border-radius:999px;padding:.08rem .5rem;margin:.12rem;font-family:monospace">SSN</span>
    <span style="display:inline-block;font-size:.72rem;border:1px solid #cc7a00;color:#cc7a00;border-radius:999px;padding:.08rem .5rem;margin:.12rem;font-family:monospace">Race</span>
    <span style="display:inline-block;font-size:.72rem;border:1px solid #cc7a00;color:#cc7a00;border-radius:999px;padding:.08rem .5rem;margin:.12rem;font-family:monospace">Age</span>
    <span style="display:inline-block;font-size:.72rem;border:1px solid #cc7a00;color:#cc7a00;border-radius:999px;padding:.08rem .5rem;margin:.12rem;font-family:monospace">Sex</span>
    <span style="display:inline-block;font-size:.72rem;border:1px solid rgba(128,128,128,.4);border-radius:999px;padding:.08rem .5rem;margin:.12rem;font-family:monospace">Scientist&nbsp;ID</span>
    <span style="font-size:.78rem;opacity:.72;font-style:italic">&nbsp; attribute $\bw$ &mdash; orange entries must stay private</span>
  </div>
  <div style="font-size:.78rem;opacity:.72;font-style:italic;margin-top:.35rem">policy example &nbsp; $P:\ (5000 &lt; \text{SSN} &lt; 8000)\ \wedge\ (\text{Infectious Disease Specialist})$</div>
</div>

Inner-product predicates are expressive — they capture disjunctions, polynomials, and CNF/DNF formulae — so this single primitive covers a lot of "search-then-aggregate" tasks. But deploy it with existing AB-IPFE and you hit two walls.

<div style="display:flex;gap:1.5rem;flex-wrap:wrap;justify-content:center;margin:1.4rem 0">
  <div style="flex:1 1 260px;max-width:380px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05)" markdown="1">
  <div style="font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8;text-align:center;font-weight:700;margin-bottom:.5rem">Wall 1 — bounded sizes</div>

  Existing AB-IPFE fixes a maximum data/attribute length **at setup**. The MoH cannot guess that bound in advance, and once chosen, the master public key — and *every* ciphertext a hospital ever produces — grows with the bound, even when the actual record is tiny.
  </div>
  <div style="flex:1 1 260px;max-width:380px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05)" markdown="1">
  <div style="font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8;text-align:center;font-weight:700;margin-bottom:.5rem">Wall 2 — leaked attributes</div>

  Existing AB-IPFE reveals the attribute $\bw$ attached to a ciphertext. That hands the SSN, age, and other identifiers straight to the data user — a privacy breach the hospital cannot allow.
  </div>
</div>

Our work removes both walls at once with **unbounded, attribute-hiding predicate IPFE (UP-IPFE)**: no size bound at setup, and the ciphertext both *hides* $\bw$ and grows only with the data and attributes actually present.

## What UP-IPFE Computes

A ciphertext $\CT_{\bx,\bw}$ carries a message $\bx$ and attribute $\bw$; a secret key $\SK_{\by,\bv}$ carries a key vector $\by$ and a predicate vector $\bv$. Decryption returns $\ip{\bx}{\by}$ — but only when two conditions line up: an **index-set relation** and a **predicate** $R(\bw,\bv)$.

<div style="margin:1.5rem 0;text-align:center">
  <div style="display:flex;align-items:center;justify-content:center;gap:.6rem;flex-wrap:wrap">
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">$\CT_{\bx,\bw}$</div>
    <div style="font-size:1.1rem;opacity:.6">$+$</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">$\SK_{\by,\bv}$</div>
    <div style="font-size:1.2rem;opacity:.55">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">index relation holds?<br><small style="font-weight:400;opacity:.72">permissive / strict</small></div>
    <div style="font-size:1.2rem;opacity:.55">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">$R(\bw,\bv)=1$?<br><small style="font-weight:400;opacity:.72">zero / non-zero</small></div>
    <div style="font-size:1.2rem;opacity:.55">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid #2e9e5b;color:#2e9e5b;border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">output $\ip{\bx}{\by}$</div>
  </div>
</div>

Because vectors are unbounded, they live on index sets, and two flavours of compatibility matter:

<div style="display:flex;gap:1.5rem;flex-wrap:wrap;justify-content:center;margin:1.4rem 0">
  <div style="flex:1 1 260px;max-width:380px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05)" markdown="1">
  <div style="font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8;text-align:center;font-weight:700;margin-bottom:.5rem">Index relation</div>

  - **Permissive** $\Rp$: defined when $I_{\bv}\subseteq I_{\bw}$, summing over the smaller set.
  - **Strict** $\Rs$: defined only when $I_{\bw}=I_{\bv}$.
  </div>
  <div style="flex:1 1 260px;max-width:380px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05)" markdown="1">
  <div style="font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8;text-align:center;font-weight:700;margin-bottom:.5rem">Predicate $R(\bw,\bv)$</div>

  - **Zero** (UZP-IPFE): accept iff $\ip{\bw}{\bv}=0$.
  - **Non-zero** (UNP-IPFE): accept iff $\ip{\bw}{\bv}\neq 0$.
  </div>
</div>

The security goal is **attribute-hiding**: an adversary holding many keys — both *accepting* and *non-accepting* with respect to the challenge — should learn nothing beyond $\ip{\bx}{\by}$ (and not even the attribute $\bw$). *Full* attribute-hiding allows arbitrary accepting and non-accepting keys; *weak* attribute-hiding restricts the predicate vectors of accepting keys so the attribute can't simply be read off. The paper delivers two schemes at complementary points of this space.

## Construction 1 — UZP-IPFE (the technical heart)

The first scheme is a **public-key**, permissive, **zero-predicate** UP-IPFE with **full attribute-hiding** in the standard model under $\sxdh$. Its starting point is the unbounded IPFE of Tomida and Takashima (TT18), whose index-encoding trick supplies both the unboundedness and the entropy needed to block illegitimate keys. Getting from there to an attribute-hiding predicate scheme takes a careful, four-step climb.

<div style="display:flex;flex-direction:column;gap:.5rem;max-width:620px;margin:1.4rem auto">
  <div style="display:flex;align-items:center;gap:.7rem;border:1px solid rgba(128,128,128,.35);border-radius:9px;padding:.5rem .8rem;background:rgba(128,128,128,.05)"><span style="flex:0 0 auto;width:1.6rem;height:1.6rem;border-radius:50%;display:flex;align-items:center;justify-content:center;font-weight:700;color:#fff;font-size:.85rem;background:#cc7a00">1</span><span><b>Concatenate &amp; randomize</b> &mdash; <small style="opacity:.75">one TT18 call; randomize to mask $\ip{\bx}{\by}$ when $\ip{\bw}{\bv}\neq 0$.</small></span></div>
  <div style="display:flex;align-items:center;gap:.7rem;border:1px solid rgba(128,128,128,.35);border-radius:9px;padding:.5rem .8rem;background:rgba(128,128,128,.05)"><span style="flex:0 0 auto;width:1.6rem;height:1.6rem;border-radius:50%;display:flex;align-items:center;justify-content:center;font-weight:700;color:#fff;font-size:.85rem;background:#cc3a2f">2</span><span><b>Two independent calls</b> &mdash; <small style="opacity:.75">fixes permissiveness, but opens a mix-n-match attack.</small></span></div>
  <div style="display:flex;align-items:center;gap:.7rem;border:1px solid rgba(128,128,128,.35);border-radius:9px;padding:.5rem .8rem;background:rgba(128,128,128,.05)"><span style="flex:0 0 auto;width:1.6rem;height:1.6rem;border-radius:50%;display:flex;align-items:center;justify-content:center;font-weight:700;color:#fff;font-size:.85rem;background:#2e9e5b">3</span><span><b>A middle route</b> &mdash; <small style="opacity:.75">two calls bound by a joint secret-sharing of zero.</small></span></div>
  <div style="display:flex;align-items:center;gap:.7rem;border:1px solid rgba(128,128,128,.35);border-radius:9px;padding:.5rem .8rem;background:rgba(128,128,128,.05)"><span style="flex:0 0 auto;width:1.6rem;height:1.6rem;border-radius:50%;display:flex;align-items:center;justify-content:center;font-weight:700;color:#fff;font-size:.85rem;background:#2e9e5b">4</span><span><b>Separate bases</b> &mdash; <small style="opacity:.75">different DPVS bases to stop cross-pairing.</small></span></div>
</div>

### Step 1 — concatenate, then randomize

Pack $(\bx,\bw)$ as the message and $(\by,\bv)$ as the key into a single TT18 ciphertext and key. Correctness hands you the **sum** $\ip{\bw}{\bv} + \ip{\bx}{\by}$ — which is exactly $\ip{\bx}{\by}$ when $\ip{\bw}{\bv}=0$. To stop a non-accepting key ($\ip{\bw}{\bv}\neq 0$) from leaking, scale the attribute side by fresh randomness $\delta,\omega$:

<div style="margin:1.4rem 0;text-align:center">
  <div style="display:flex;align-items:center;justify-content:center;gap:.5rem;flex-wrap:wrap">
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">TT18 decrypt</div>
    <div style="font-size:1.2rem;opacity:.55">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">$\delta\omega\,\ip{\bw}{\bv} + \ip{\bx}{\by}$</div>
  </div>
  <div style="display:flex;align-items:center;justify-content:center;gap:1.4rem;flex-wrap:wrap;margin-top:.7rem">
    <div style="display:inline-block;border:1.5px solid #2e9e5b;color:#2e9e5b;border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">$\ip{\bw}{\bv}=0\ \Rightarrow\ \ip{\bx}{\by}$<br><small style="font-weight:400;opacity:.72">accepting: statistic revealed</small></div>
    <div style="display:inline-block;border:1.5px dashed var(--global-theme-color,#0076df);border-radius:10px;padding:.5rem .85rem;font-weight:600;background:rgba(128,128,128,.06)">$\ip{\bw}{\bv}\neq0\ \Rightarrow\ \text{masked}$<br><small style="font-weight:400;opacity:.72">non-accepting: nothing leaks</small></div>
  </div>
</div>

Good intuition — but concatenation quietly **breaks permissiveness** (the merged index set no longer splits into the $\bx$- and $\bw$-parts), and the mask won't line up across challenge attributes for non-accepting keys. Dead end.

### Step 2 — two independent calls (and a new attack)

Encrypt $\bw$ and $\bx$ with **two** TT18 calls, and let the key be two TT18 keys, for $(\bv,I_{\bv})$ and $(\by,I_{\by})$. Permissiveness now holds on each side — but independence is too much freedom:

<div style="border-left:4px solid #cc3a2f;background:rgba(204,58,47,.08);padding:.7rem 1.05rem;border-radius:0 8px 8px 0;margin:1.2rem 0">
<b style="color:#cc3a2f">Mix-n-match attack.</b> From keys $\SK_{\bv,\by}=(\mathsf{sk}_{\bv},\mathsf{sk}_{\by})$ and $\SK_{\bv',\by'}=(\mathsf{sk}_{\bv'},\mathsf{sk}_{\by'})$, an adversary splices a brand-new legitimate-looking key $\SK_{\bv,\by'}=(\mathsf{sk}_{\bv},\mathsf{sk}_{\by'})$ — recombining a predicate from one key with a function from another, breaking security.
</div>

### Step 3 — a middle route: bind the two halves

The fix is a hybrid: still two parallel TT18 calls, but **no longer independent**. Tie the two key halves together with a *joint secret-sharing of zero* — shares $\lbrace \gamma_i,\widetilde{\gamma}_j\rbrace$ with $\sum_i\gamma_i+\sum_j\widetilde{\gamma}_j=0$ — and let the two ciphertext halves share a *common randomness* $z$ so the shares recombine exactly at decryption.

<div style="display:flex;gap:1.5rem;flex-wrap:wrap;justify-content:center;margin:1.3rem 0">
  <div style="flex:1 1 260px;max-width:360px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:.9rem 1rem;background:rgba(128,128,128,.05);text-align:center">
    <div style="font-size:.8rem;text-transform:uppercase;letter-spacing:.04em;opacity:.8;font-weight:700;margin-bottom:.5rem">secret key — bound by $\sum\gamma=0$</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:9px;padding:.4rem .7rem;font-weight:600;margin:.2rem">$\mathsf{sk}_{\by}$ &nbsp;<small style="font-weight:400;opacity:.7">share $\gamma_i$</small></div>
    <div style="font-size:.95rem;color:var(--global-theme-color,#0076df);font-weight:700">&#8597; joint zero-share</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:9px;padding:.4rem .7rem;font-weight:600;margin:.2rem">$\mathsf{sk}_{\bv}$ &nbsp;<small style="font-weight:400;opacity:.7">share $\widetilde{\gamma}_j$</small></div>
    <div style="font-size:.76rem;opacity:.72;font-style:italic;margin-top:.4rem">splicing a foreign half breaks the zero-sum &rarr; mix-n-match blocked</div>
  </div>
  <div style="flex:1 1 260px;max-width:360px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:.9rem 1rem;background:rgba(128,128,128,.05);text-align:center">
    <div style="font-size:.8rem;text-transform:uppercase;letter-spacing:.04em;opacity:.8;font-weight:700;margin-bottom:.5rem">ciphertext — shared $z$</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:9px;padding:.4rem .7rem;font-weight:600;margin:.2rem">$\mathsf{ct}_{\bx}$ &nbsp;<small style="font-weight:400;opacity:.7">randomness $z$</small></div>
    <div style="font-size:.95rem;color:var(--global-theme-color,#0076df);font-weight:700">&#8597; common $z$</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:9px;padding:.4rem .7rem;font-weight:600;margin:.2rem">$\mathsf{ct}_{\bw}$ &nbsp;<small style="font-weight:400;opacity:.7">randomness $z$</small></div>
    <div style="font-size:.76rem;opacity:.72;font-style:italic;margin-top:.4rem">lets the holder recombine the shares at decryption</div>
  </div>
</div>

Concretely, in the dual pairing vector space (DPVS), with $\dbi{\cdot}$ / $\dbii{\cdot}$ denoting encodings in the two source groups:

$$
\SK_{\by,\bv}:\quad
\dbii{\bk_i=(\rho_i(-i,1),\,y_i,\,\gamma_i)\bB^{\ast}}\ \ \text{and}\ \
\dbii{\bk_j=(\widetilde{\rho}_j(-j,1),\,\omega v_j,\,\widetilde{\gamma}_j)\bB^{\ast}}
\quad\text{s.t. }\textstyle\sum_i\gamma_i+\sum_j\widetilde{\gamma}_j=0,
$$

$$
\CT_{\bx,\bw}:\quad
\dbi{\bc_i=(\pi_i(1,i),\,x_i,\,z)\bB}\ \ \text{and}\ \
\dbi{\bc_j=(\widetilde{\pi}_j(1,j),\,\delta w_j,\,z)\bB}.
$$

The first two coordinates encode the index (the source of unboundedness), the third the vector entry, and the last the binding randomness.

### Step 4 — separate bases stop cross-pairing

One subtlety remains: with overlapping index sets, an adversary could pair $\bk_i$ with $\bc_j$ and $\bk_j$ with $\bc_i$ — crossing the wires — to obtain $\delta\ip{\bw}{\by}+\omega\ip{\bx}{\bv}$ and leak information about $\bx$.

<div style="margin:1.3rem 0;text-align:center">
  <div style="display:flex;align-items:center;justify-content:center;gap:1.6rem;flex-wrap:wrap">
    <div>
      <div style="display:inline-block;border:1.5px solid #cc3a2f;color:#cc3a2f;border-radius:10px;padding:.5rem .8rem;font-weight:600;background:rgba(204,58,47,.06)">same basis $\bB$ everywhere<br><small style="font-weight:400">$\bk_i\times\bc_j,\ \bk_j\times\bc_i$ pair up &rarr; leak</small></div>
    </div>
    <div style="font-size:1.3rem;opacity:.55">&rArr;</div>
    <div>
      <div style="display:inline-block;border:1.5px solid #2e9e5b;color:#2e9e5b;border-radius:10px;padding:.5rem .8rem;font-weight:600;background:rgba(46,158,91,.06)">$(\bB,\bB^{\ast})$ for $(\bx,\by)$<br>$(\widetilde{\bB},\widetilde{\bB}^{\ast})$ for $(\bw,\bv)$<br><small style="font-weight:400">cross-pairings land in incompatible spaces &rarr; vanish</small></div>
    </div>
  </div>
</div>

The result is a full attribute-hiding UZP-IPFE that protects message *and* attribute against an adversary as strong as a TT18 adversary **and** a full attribute-hiding zero-predicate adversary combined — achieved by extending the TT18 framework from "hiding an unbounded message" to "hiding an unbounded message *and* attribute."

## Construction 2 — UNP-IPFE (simple, generic, succinct keys)

The second scheme flips several choices: **secret-key**, strict, **non-zero-predicate**, with **weak** attribute-hiding in the stronger **simulation** model under bilateral $k$-Lin. Its headline feature is **constant-size secret keys** — independent of the (unbounded) vector lengths.

The route is a classic transformation: encode the attribute alongside the payload, and recover the payload only when $\ip{\bw}{\bv}\neq 0$. Here the "payload" is itself an inner product, so encrypt the **tensor** $\bx\otimes\bw$:

<div style="margin:1.3rem 0;text-align:center">
  <div style="display:flex;align-items:center;justify-content:center;gap:.5rem;flex-wrap:wrap">
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .8rem;font-weight:600;background:rgba(128,128,128,.06)">encrypt $\bx\otimes\bw$<br><small style="font-weight:400;opacity:.72">via UQFE (compact)</small></div>
    <div style="font-size:1.2rem;opacity:.55">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.5rem .8rem;font-weight:600;background:rgba(128,128,128,.06)">$\ip{\bx}{\by}\cdot\ip{\bw}{\bv}$ &nbsp;and&nbsp; $\ip{\bw}{\bv}$</div>
    <div style="font-size:1.2rem;opacity:.55">&rarr;</div>
    <div style="display:inline-block;border:1.5px solid #2e9e5b;color:#2e9e5b;border-radius:10px;padding:.5rem .8rem;font-weight:600;background:rgba(128,128,128,.06)">divide out $\ip{\bw}{\bv}$<br><small style="font-weight:400;opacity:.72">get $\ip{\bx}{\by}$ if $\ip{\bw}{\bv}\neq0$</small></div>
  </div>
</div>

The identity $\ip{\bx\otimes\bw}{\by\otimes\bv}=\ip{\bx}{\by}\cdot\ip{\bw}{\bv}$ does the work. The snag is that a tensor naively makes the ciphertext **quadratic** in the vector lengths; computing that quadratic term with an **unbounded quadratic FE (UQFE)** instead keeps the ciphertext **linear**:

<div style="border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0" markdown="1">
$$
\SK_{\by,\bv}:\ \mathsf{qsk}_{\by\otimes\bv},\ \mathsf{isk}_{\bv}
\qquad\qquad
\CT_{\bx,\bw}:\ \mathsf{qct}_{\bx,\bw},\ \mathsf{ict}_{\bw}
$$
A UQFE handles the quadratic $\bx\otimes\bw$ part with compact ciphertexts; a plain unbounded IPFE recovers $\ip{\bw}{\bv}$.
</div>

This yields a simulation-secure UNP-IPFE with compact ciphertexts and constant-size keys — and motivates a question the paper also takes up: building a **simulation-secure UQFE** with constant keys and compact ciphertexts, since the only prior UQFE is indistinguishability-based with linear-size keys.

## The Two Schemes at a Glance

<div style="overflow-x:auto;margin:1rem 0">
<table style="border-collapse:collapse;font-size:.74rem;line-height:1.35;margin:0 auto">
  <thead>
    <tr>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:center">Scheme</th>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:center">Setting</th>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:center">Index relation</th>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:center">Predicate</th>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:center">Attribute-hiding</th>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:center">Assumption</th>
      <th style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;background:rgba(128,128,128,.12);white-space:nowrap;text-align:center">Notable</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center;font-weight:700">UZP-IPFE</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">public key</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">permissive</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">zero ($\ip{\bw}{\bv}=0$)</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">full, IND, semi-adaptive</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">SXDH</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">standard model</td>
    </tr>
    <tr>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center;font-weight:700">UNP-IPFE</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">secret key</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">strict</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">non-zero ($\ip{\bw}{\bv}\neq0$)</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">weak, SIM, semi-adaptive</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">bilateral $k$-Lin</td>
      <td style="border:1px solid rgba(128,128,128,.35);padding:.3rem .5rem;white-space:nowrap;text-align:center">constant-size keys</td>
    </tr>
  </tbody>
</table>
</div>

## Why It Matters

Back to the hospital. With UP-IPFE, the MoH never fixes a size bound at setup, so hospitals encrypt records of whatever size they have, and ciphertexts stay proportional to the actual data. The sensitive attributes — SSN, age, sex — are **hidden** inside the ciphertext, yet a research centre with the right policy key still extracts exactly the authorized statistic $\ip{\bx}{\by}$ and nothing more.

To our knowledge these are the first unbounded AB-IPFE schemes that are simultaneously **attribute-hiding** and **unbounded** — one maximizing security (full attribute-hiding in the standard model), the other maximizing succinctness (constant-size keys, simulation security) — turning a clean theoretical primitive into something that fits the messy, unbounded shape of real data.

<div style="border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0" markdown="1">
Want the formal definitions, the dual-system security proofs, and the full UQFE discussion? They're in the paper {% cite dowerah2023unbounded %} and at [ePrint 2023/483](https://ia.cr/2023/483).
</div>
