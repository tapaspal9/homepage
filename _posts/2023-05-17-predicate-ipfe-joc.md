---
## layout: post
title: “Computing on Unbounded Data with Predicates”
date: 2023-05-17
description: Evaluating weighted statistics on encrypted medical records — gated by a hidden policy, with no preset bound on data or attribute size. The ideas behind unbounded, attribute-hiding predicate IPFE from pairings.
tags: functional-encryption predicate-encryption pairings
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

<style>
.pipfe-fig{margin:1.6rem 0;text-align:center}
.pipfe-node{display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.55rem .9rem;margin:.25rem;font-weight:600;background:rgba(128,128,128,.06)}
.pipfe-node small{font-weight:400;opacity:.72}
.pipfe-node--accent{border-color:var(--global-theme-color,#0076df);color:var(--global-theme-color,#0076df)}
.pipfe-node--good{border-color:#2e9e5b;color:#2e9e5b}
.pipfe-node--warn{border-color:#cc7a00;color:#cc7a00}
.pipfe-node--dashed{border-style:dashed;border-color:var(--global-theme-color,#0076df)}
.pipfe-arrow{font-size:1.2rem;opacity:.55;line-height:1;margin:.1rem}
.pipfe-flowrow{display:flex;align-items:center;justify-content:center;gap:.5rem;flex-wrap:wrap;margin:1.1rem 0}
.pipfe-edgelabel{font-size:.78rem;opacity:.72;font-style:italic;padding:0 .15rem}
.pipfe-twocol{display:flex;gap:1.5rem;flex-wrap:wrap;justify-content:center;margin:1.4rem 0}
.pipfe-panel{flex:1 1 260px;max-width:380px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05);text-align:left}
.pipfe-panel h4{margin:.1rem 0 .55rem;font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.8;text-align:center}
.pipfe-chip{display:inline-block;font-size:.72rem;border:1px solid rgba(128,128,128,.4);border-radius:999px;padding:.08rem .5rem;margin:.12rem;background:rgba(128,128,128,.07);font-family:monospace}
.pipfe-chip--hide{border-color:#cc7a00;color:#cc7a00}
.pipfe-callout{border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0}
.pipfe-attack{border-left:4px solid #cc3a2f;background:rgba(204,58,47,.08);padding:.7rem 1.05rem;border-radius:0 8px 8px 0;margin:1.2rem 0}
.pipfe-attack b{color:#cc3a2f}
.pipfe-ladder{display:flex;flex-direction:column;gap:.5rem;max-width:600px;margin:1.4rem auto}
.pipfe-rung{display:flex;align-items:center;gap:.7rem;border:1px solid rgba(128,128,128,.35);border-radius:9px;padding:.5rem .8rem;background:rgba(128,128,128,.05)}
.pipfe-badge{flex:0 0 auto;width:1.6rem;height:1.6rem;border-radius:50%;display:flex;align-items:center;justify-content:center;font-weight:700;color:#fff;font-size:.85rem}
.pipfe-badge--good{background:#2e9e5b}.pipfe-badge--warn{background:#cc7a00}.pipfe-badge--bad{background:#cc3a2f}
.pipfe-rung small{opacity:.75}
.pipfe-table{width:100%;border-collapse:collapse;margin:1rem 0;font-size:.86rem}
.pipfe-table th,.pipfe-table td{border:1px solid rgba(128,128,128,.35);padding:.45rem .6rem;text-align:center}
.pipfe-table th{background:rgba(128,128,128,.12)}
.pipfe-table td:first-child{font-weight:700}
</style>

<div class="pipfe-callout" markdown="1">
Based on joint work with **Uddipana Dowerah, Subhranil Dutta, Aikaterini Mitrokotsa, and Sayantan Mukherjee** — *Unbounded Predicate Inner Product Functional Encryption from Pairings* {% cite dowerah2023unbounded %}. This post is an intuitive tour of the ideas; the paper carries the full constructions and proofs.
</div>

## A Hospital, a Cloud, and a Statistic

Picture the Ministry of Health (MoH) wanting the average blood pressure of patients recently treated for influenza — computed over records that hospitals have **encrypted** and parked in a cloud. The MoH should learn that one statistic, and *only* for the patients matching a policy, without ever seeing the raw records or the sensitive identifiers attached to them.

This is exactly what **attribute-based inner-product FE (AB-IPFE)** promises. A hospital encrypts a patient’s vitals $\bx$ (temperature, heart rate, blood pressure, …) under an **attribute** $\bw$ (SSN, age, sex, eligible designations, …). A research centre holds a key for a **policy** $P$ and a **weight vector** $\by$. Decryption reveals the weighted statistic $\ip{\bx}{\by}$ — but only if the attribute satisfies the policy.

<div class="pipfe-fig">
  <div class="pipfe-flowrow">
    <div class="pipfe-node">Hospital <small>(data owner)</small><br><small>encrypts vitals $\bx$ under attribute $\bw$</small></div>
    <div class="pipfe-arrow">&rarr;</div>
    <div class="pipfe-node pipfe-node--dashed">$\CT_{\bx,\bw}$ on the cloud<br><small>attribute $\bw$ stays hidden</small></div>
  </div>
  <div class="pipfe-flowrow">
    <div class="pipfe-node">MoH <small>(authority)</small><br><small>issues $\SK_{P,\by}$</small></div>
    <div class="pipfe-arrow">&rarr;</div>
    <div class="pipfe-node">Specialist <small>(data user)</small></div>
    <div class="pipfe-arrow">&rarr;</div>
    <div class="pipfe-node pipfe-node--good">learns $\ip{\bx}{\by}$<br><small>iff $P(\bw)$ holds</small></div>
  </div>
  <div style="margin-top:.4rem">
    <span class="pipfe-chip pipfe-chip--hide">SSN</span>
    <span class="pipfe-chip pipfe-chip--hide">Race</span>
    <span class="pipfe-chip pipfe-chip--hide">Age</span>
    <span class="pipfe-chip pipfe-chip--hide">Sex</span>
    <span class="pipfe-chip">Scientist&nbsp;ID</span>
    <span class="pipfe-edgelabel">&nbsp; attribute $\bw$ &mdash; orange entries must stay private</span>
  </div>
  <div class="pipfe-edgelabel" style="margin-top:.3rem">policy example &nbsp; $P:\ (5000 &lt; \text{SSN} &lt; 8000)\ \wedge\ (\text{Infectious Disease Specialist})$</div>
</div>

Inner-product predicates are expressive — they capture disjunctions, polynomials, and CNF/DNF formulae — so this single primitive covers a lot of “search-then-aggregate” tasks. But deploy it with existing AB-IPFE and you hit two walls.

<div class="pipfe-twocol">
  <div class="pipfe-panel" markdown="1">
  <h4>Wall 1 — bounded sizes</h4>

Existing AB-IPFE fixes a maximum data/attribute length **at setup**. The MoH cannot guess that bound in advance, and once chosen, the master public key — and *every* ciphertext a hospital ever produces — grows with the bound, even when the actual record is tiny.

  </div>
  <div class="pipfe-panel" markdown="1">
  <h4>Wall 2 — leaked attributes</h4>

Existing AB-IPFE reveals the attribute $\bw$ attached to a ciphertext. That hands the SSN, age, and other identifiers straight to the data user — a privacy breach the hospital cannot allow.

  </div>
</div>

Our work removes both walls at once with **unbounded, attribute-hiding predicate IPFE (UP-IPFE)**: no size bound at setup, and the ciphertext both *hides* $\bw$ and grows only with the data and attributes actually present.

## What UP-IPFE Computes

A ciphertext $\CT_{\bx,\bw}$ carries a message $\bx$ and attribute $\bw$; a secret key $\SK_{\by,\bv}$ carries a key vector $\by$ and a predicate vector $\bv$. Decryption returns $\ip{\bx}{\by}$ — but only when two conditions line up: an **index-set relation** and a **predicate** $R(\bw,\bv)$.

Because vectors are unbounded, they live on index sets, and two flavours of compatibility matter:

<div class="pipfe-twocol">
  <div class="pipfe-panel" markdown="1">
  <h4>Index relation</h4>

- **Permissive** $\Rp$: defined when $I_{\bv}\subseteq I_{\bw}$, summing over the smaller set.
- **Strict** $\Rs$: defined only when $I_{\bw}=I_{\bv}$.

  </div>
  <div class="pipfe-panel" markdown="1">
  <h4>Predicate $R(\bw,\bv)$</h4>

- **Zero** (UZP-IPFE): accept iff $\ip{\bw}{\bv}=0$.
- **Non-zero** (UNP-IPFE): accept iff $\ip{\bw}{\bv}\neq 0$.

  </div>
</div>

The security goal is **attribute-hiding**: an adversary holding many keys — both *accepting* and *non-accepting* with respect to the challenge — should learn nothing beyond $\ip{\bx}{\by}$ (and not even the attribute $\bw$). *Full* attribute-hiding allows arbitrary accepting and non-accepting keys; *weak* attribute-hiding restricts the predicate vectors of accepting keys so the attribute can’t simply be read off. The paper delivers two schemes sitting at different, complementary points of this design space.

## Construction 1 — UZP-IPFE (the technical heart)

The first scheme is a **public-key**, permissive, **zero-predicate** UP-IPFE with **full attribute-hiding** in the standard model under $\sxdh$. Its starting point is the unbounded IPFE of Tomida and Takashima (TT18), whose index-encoding trick supplies both the unboundedness and the entropy needed to block illegitimate keys. Getting from there to a *attribute-hiding* predicate scheme takes a careful, four-step climb.

<div class="pipfe-ladder">
  <div class="pipfe-rung"><span class="pipfe-badge pipfe-badge--warn">1</span><span><b>Concatenate &amp; randomize</b> &mdash; <small>one TT18 call on $(\bx,\bw)$, $(\by,\bv)$; randomize to hide $\ip{\bx}{\by}$ when $\ip{\bw}{\bv}\neq 0$.</small></span></div>
  <div class="pipfe-rung"><span class="pipfe-badge pipfe-badge--bad">2</span><span><b>Two independent calls</b> &mdash; <small>fixes permissiveness, but opens a mix-n-match attack.</small></span></div>
  <div class="pipfe-rung"><span class="pipfe-badge pipfe-badge--good">3</span><span><b>A middle route</b> &mdash; <small>two calls bound by a joint secret-sharing of zero.</small></span></div>
  <div class="pipfe-rung"><span class="pipfe-badge pipfe-badge--good">4</span><span><b>Separate bases</b> &mdash; <small>different DPVS bases to stop cross-pairing.</small></span></div>
</div>

### Step 1 — concatenate, then randomize

Pack $(\bx,\bw)$ as the message and $(\by,\bv)$ as the key into a single TT18 ciphertext and key. Correctness of TT18 hands you the **sum**

$$
\ip{\bw}{\bv} + \ip{\bx}{\by},
$$

which is exactly $\ip{\bx}{\by}$ when $\ip{\bw}{\bv}=0$. But when $\ip{\bw}{\bv}\neq 0$, that sum is distinguishable — leaking that the key is non-accepting. The fix is to scale the attribute side by fresh randomness $\delta,\omega$, turning the sum into

$$
\delta\omega,\ip{\bw}{\bv} + \ip{\bx}{\by},
$$

so a non-zero $\ip{\bw}{\bv}$ now *masks* $\ip{\bx}{\by}$. Good intuition — but concatenation quietly **breaks permissiveness** (the combined index set no longer splits cleanly into the $\bx$- and $\bw$-parts), and the masking trick won’t line up across the two challenge attributes for non-accepting keys. Concatenation is a dead end.

### Step 2 — two independent calls (and a new attack)

Encrypt $\bw$ and $\bx$ with **two** TT18 calls, and let the key be two TT18 keys, for $(\bv,I_{\bv})$ and $(\by,I_{\by})$. Now permissiveness holds separately on each side. But independence is too much freedom:

<div class="pipfe-attack" markdown="1">
**Mix-n-match attack.** From keys $\SK_{\bv,\by}=(\mathsf{sk}_{\bv},\mathsf{sk}_{\by})$ and $\SK_{\bv',\by'}=(\mathsf{sk}_{\bv'},\mathsf{sk}_{\by'})$, an adversary can splice together a brand-new legitimate-looking key $\SK_{\bv,\by'}=(\mathsf{sk}_{\bv},\mathsf{sk}_{\by'})$ — recombining a predicate from one key with a function from another, and breaking security.
</div>

### Step 3 — a middle route: bind the two halves

The fix is a hybrid: still two parallel TT18 calls, but **no longer independent**. We tie the two key halves together with a *joint secret-sharing of zero* — a set $\mathcal{S}=\lbrace \gamma_i,\widetilde{\gamma}_j\rbrace$ whose shares satisfy $\sum_i\gamma_i+\sum_j\widetilde{\gamma}*j=0$ — and let the two ciphertext halves share a *common randomness* $z$ so the shares recombine exactly at decryption. Concretely, in the dual pairing vector space (DPVS), with $\db{\cdot}*\iota$ denoting an encoding in group $\iota$:

$$
\SK_{\by,\bv}:\quad
\dbii{\bk_i=(\rho_i(-i,1),,y_i,,\gamma_i)\bB^*}\ \ \text{and}\   
\dbii{\bk_j=(\widetilde{\rho}_j(-j,1),,\omega v_j,,\widetilde{\gamma}_j)\bB^*}
\quad\text{s.t. }\textstyle\sum_i\gamma_i+\sum_j\widetilde{\gamma}_j=0,
$$

$$
\CT_{\bx,\bw}:\quad
\dbi{\bc_i=(\pi_i(1,i),,x_i,,z)\bB}\ \ \text{and}\   
\dbi{\bc_j=(\widetilde{\pi}_j(1,j),,\delta w_j,,z)\bB}.
$$

The first two coordinates encode the index (the source of unboundedness), the third the vector entry, and the last the binding randomness. The shared zero-sum locks $\mathsf{sk}*{\by}$ and $\mathsf{sk}*{\bv}$ together — a spliced key no longer reconstructs the secret, killing the mix-n-match attack.

### Step 4 — separate bases stop cross-pairing

One subtlety remains. With overlapping index sets, an adversary could pair $\bk_i$ with $\bc_j$ and $\bk_j$ with $\bc_i$ — crossing the wires — to obtain $\delta\ip{\bw}{\by}+\omega\ip{\bx}{\bv}$ and leak unwanted information about $\bx$.

<div class="pipfe-callout" markdown="1">
**Fix.** Encode the message side $(\bx,\by)$ and the attribute side $(\bw,\bv)$ under *different* DPVS bases — $(\bB,\bB^*)$ for one and $(\widetilde{\bB},\widetilde{\bB}^*)$ for the other. Cross-pairings now land in incompatible spaces and vanish.
</div>

The result is a full attribute-hiding UZP-IPFE: it must protect message *and* attribute against an adversary as strong as a TT18 adversary **and** a full attribute-hiding zero-predicate adversary combined — handled by extending the TT18 framework from “hiding an unbounded message” to “hiding an unbounded message *and* attribute.”

## Construction 2 — UNP-IPFE (simple, generic, succinct keys)

The second scheme flips several choices: it’s a **secret-key**, strict, **non-zero-predicate** UP-IPFE with **weak** attribute-hiding, proven in the stronger **simulation** model under bilateral $k$-Lin. Its headline feature is **constant-size secret keys** — independent of the (unbounded) vector lengths — which the first scheme doesn’t offer.

The route is a classic transformation: a non-zero-predicate scheme can be built from IPFE by encoding the attribute alongside the payload, recovering the payload only when $\ip{\bw}{\bv}\neq 0$. Here the “payload” is itself an inner product, so the natural move is to encrypt the **tensor** $\bx\otimes\bw$. Decryption yields

$$
\ip{\bx\otimes\bw}{\by\otimes\bv} = \ip{\bx}{\by}\cdot\ip{\bw}{\bv}
\quad\text{and}\quad \ip{\bw}{\bv},
$$

from which $\ip{\bx}{\by}$ falls out whenever $\ip{\bw}{\bv}\neq 0$. The snag: a tensor makes the ciphertext **quadratic** in the vector lengths. The cure is to compute that quadratic term with an **unbounded quadratic FE (UQFE)** instead, whose ciphertext scales only *linearly*:

<div class="pipfe-callout" markdown="1">
$$
\SK_{\by,\bv}:\ \mathsf{qsk}_{\by\otimes\bv},\ \mathsf{isk}_{\bv}
\qquad\qquad
\CT_{\bx,\bw}:\ \mathsf{qct}_{\bx,\bw},\ \mathsf{ict}_{\bw}
$$
A UQFE handles the quadratic $\bx\otimes\bw$ part with compact ciphertexts; a plain unbounded IPFE recovers $\ip{\bw}{\bv}$.
</div>

This gives a simulation-secure UNP-IPFE with compact ciphertexts and constant-size keys — and motivates a question the paper also takes up: building a **simulation-secure UQFE** with constant keys and compact ciphertexts, since the only prior UQFE is indistinguishability-based with linear-size keys.

## The Two Schemes at a Glance

<table class="pipfe-table">
  <thead>
    <tr><th>Scheme</th><th>Setting</th><th>Index relation</th><th>Predicate</th><th>Attribute-hiding</th><th>Assumption</th><th>Notable</th></tr>
  </thead>
  <tbody>
    <tr><td>UZP-IPFE</td><td>public key</td><td>permissive</td><td>zero ($\ip{\bw}{\bv}=0$)</td><td>full, IND, semi-adaptive</td><td>SXDH</td><td>standard model</td></tr>
    <tr><td>UNP-IPFE</td><td>secret key</td><td>strict</td><td>non-zero ($\ip{\bw}{\bv}\neq0$)</td><td>weak, SIM, semi-adaptive</td><td>bilateral $k$-Lin</td><td>constant-size keys</td></tr>
  </tbody>
</table>

## Why It Matters

Back to the hospital. With UP-IPFE, the MoH never fixes a size bound at setup, so hospitals encrypt records of whatever size they have, and ciphertexts stay proportional to the actual data. The sensitive attributes — SSN, age, sex — are **hidden** inside the ciphertext, yet a research centre with the right policy key still extracts exactly the authorized statistic $\ip{\bx}{\by}$ and nothing more.

To our knowledge these are the first unbounded AB-IPFE schemes that are simultaneously **attribute-hiding** and **unbounded** — one maximizing security (full attribute-hiding in the standard model), the other maximizing succinctness (constant-size keys, simulation security) — turning a clean theoretical primitive into something that fits the messy, unbounded shape of real data.

<div class="pipfe-callout" markdown="1">
Want the formal definitions, the dual-system security proofs, and the full UQFE discussion? They're in the paper {% cite dowerah2023unbounded %}.
</div>
