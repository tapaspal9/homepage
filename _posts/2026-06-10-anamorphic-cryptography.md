---
layout: post
title: "The Art of Covert Communications"
date: 2026-06-10
description: How a ciphertext can secretly carry a second message that even a key-holding "dictator" cannot detect — the intuition behind multi-message anamorphic encryption and signatures.
tags: anamorphic post-quantum signatures lattice pairings
toc:
  sidebar: left
related_posts: false
related_publications: true
---

<div style="position:absolute; width:0; height:0; overflow:hidden;" aria-hidden="true">
$$
\newcommand{\msf}{\mathsf}
\newcommand{\mcl}{\mathcal}
\newcommand{\mbb}{\mathbb}
\newcommand{\wh}{\widehat}
\newcommand{\floor}[1]{\lfloor #1 \rfloor}
\newcommand{\seteq}{:=}
\newcommand{\samp}{\leftarrow}
\newcommand{\secp}{\lambda}
\newcommand{\G}{\mathbb{G}}
\newcommand{\Z}{\mathbb{Z}}
\newcommand{\N}{\mathbb{N}}
\newcommand{\pk}{\mathsf{pk}}
\newcommand{\sk}{\mathsf{sk}}
\newcommand{\dk}{\mathsf{dk}}
\newcommand{\tk}{\mathsf{tk}}
\newcommand{\ct}{\mathsf{ct}}
\newcommand{\act}{\mathsf{act}}
\newcommand{\apk}{\mathsf{apk}}
\newcommand{\ask}{\mathsf{ask}}
\newcommand{\avk}{\mathsf{avk}}
\newcommand{\vk}{\mathsf{vk}}
\newcommand{\td}{\mathsf{td}}
\newcommand{\gp}{\mathsf{gp}}
\newcommand{\msg}{\mathsf{msg}}
\newcommand{\amsg}{\mathsf{amsg}}  
\newcommand{\msgvec}{\mathbf{msg}}
\newcommand{\ctvec}{\mathbf{ct}}
\newcommand{\actvec}{\mathbf{act}}
\newcommand{\asigvec}{\mathbf{asig}}
\newcommand{\aGen}{\mathsf{aGen}}
\newcommand{\aenc}{\mathsf{aEnc}}
\newcommand{\adec}{\mathsf{aDec}}
\newcommand{\aGenS}{\mathsf{aGenS}}
\newcommand{\aGenR}{\mathsf{aGenR}}
\newcommand{\aSig}{\mathsf{aSig}}
\newcommand{\aDecSig}{\mathsf{aDec}}
\newcommand{\AAA}{\mathbf{A}}
\newcommand{\sss}{\mathbf{s}}
\newcommand{\ee}{\mathbf{e}}
\newcommand{\bbb}{\mathbf{b}}
\newcommand{\kkk}{\mathbf{k}}
\newcommand{\rrr}{\mathbf{r}}
\newcommand{\dlwe}{\mathsf{DLWE}}
\newcommand{\dlin}{\mathsf{DLIN}}
\newcommand{\stp}{\theta}
\newcommand{\nGen}{\mathsf{nGen}}
\newcommand{\Sen}{\mathsf{Sen}}
\newcommand{\aSen}{\mathsf{aSen}}
\newcommand{\Rec}{\mathsf{Rec}}
\newcommand{\nk}{\mathsf{nk}}
\newcommand{\anm}{\mathsf{anam}}
$$
</div>

<style>
.anam-fig{margin:1.6rem 0;text-align:center}
.anam-node{display:inline-block;border:1.5px solid rgba(128,128,128,.45);border-radius:10px;padding:.55rem .9rem;margin:.25rem;font-weight:600;background:rgba(128,128,128,.06)}
.anam-node small{font-weight:400;opacity:.7}
.anam-node--accent{border-color:var(--global-theme-color,#0076df);color:var(--global-theme-color,#0076df)}
.anam-node--hidden{border-style:dashed;border-color:var(--global-theme-color,#0076df)}
.anam-split{display:flex;justify-content:center;gap:2.5rem;flex-wrap:wrap;margin-top:.6rem}
.anam-branch{display:flex;flex-direction:column;align-items:center;gap:.2rem}
.anam-arrow{font-size:1.2rem;opacity:.55;line-height:1}
.anam-label{font-size:.8rem;opacity:.7;margin-top:.2rem;font-style:italic}
.anam-twocol{display:flex;gap:1.5rem;flex-wrap:wrap;justify-content:center;margin:1.4rem 0}
.anam-panel{flex:1 1 250px;max-width:360px;border:1px solid rgba(128,128,128,.35);border-radius:12px;padding:1rem 1.1rem;background:rgba(128,128,128,.05);text-align:left}
.anam-panel h4{margin:.1rem 0 .6rem;font-size:.82rem;text-transform:uppercase;letter-spacing:.05em;opacity:.75}
.anam-stream{display:flex;gap:.4rem;justify-content:center;flex-wrap:wrap;margin:1rem 0;font-family:monospace}
.anam-cell{width:2.2rem;height:2.6rem;display:flex;align-items:center;justify-content:center;border:1.5px solid rgba(128,128,128,.45);border-radius:6px;font-weight:700}
.anam-cell--sig{border-color:var(--global-theme-color,#0076df);color:var(--global-theme-color,#0076df)}
.anam-cell--hid{border-style:dashed;border-color:var(--global-theme-color,#0076df)}
.anam-callout{border-left:4px solid var(--global-theme-color,#0076df);background:rgba(128,128,128,.07);padding:.8rem 1.1rem;border-radius:0 8px 8px 0;margin:1.4rem 0}
.anam-table{width:100%;border-collapse:collapse;margin:1rem 0;font-size:.88rem}
.anam-table th,.anam-table td{border:1px solid rgba(128,128,128,.35);padding:.4rem .6rem;text-align:center}
.anam-table th{background:rgba(128,128,128,.12)}
.anam-table .grp{text-align:left;font-weight:700;background:rgba(128,128,128,.18)}
</style>

<div class="anam-callout" markdown="1">
Based on joint work with **Shalini Banerjee, Andy Rupp, and Daniel Slamanig** — *Simple Asymmetric Anamorphic Encryption and Signature using Multi-Message Extensions*, [Cryptology ePrint Archive 2025/370](https://eprint.iacr.org/2025/370) and appeared at Crypto {% cite banerjee2026anamorphic %}. This post is an intuitive tour of the ideas; the paper has the full constructions and proofs.
</div>

## Hidden Messages in Plain Sight

What if an encrypted message could carry **another** secret message — one that only a single chosen receiver can ever see?

That is the question behind *anamorphic cryptography*. Ordinary encryption hides the **content** of a message, but it openly admits that communication is happening. Anamorphic encryption adds a second, invisible layer of meaning to the very same ciphertext. The concept was introduced by [Giuseppe Persiano, Duong Hieu Phan, and Moti Yung](https://ia.cr/2022/639) at Eurocrypt 2022.

A useful picture: normal encryption is a **locked box**. Anyone with the key opens it and finds the message. Anamorphic encryption is a locked box with a **hidden compartment** built into the lid. Open it the usual way and you get the ordinary contents. But a receiver who knows the compartment is there — and holds a special *double key* — can quietly retrieve something else entirely.

<div class="anam-fig">
  <div class="anam-node anam-node--accent">Ciphertext of Anamorphic Encryption</div>
  <div class="anam-split">
    <div class="anam-branch">
      <div class="anam-arrow">&darr;</div>
      <div class="anam-node">Normal decryption<br><small>normal secret key</small></div>
      <div class="anam-arrow">&darr;</div>
      <div class="anam-node">Message&nbsp;<em>M</em></div>
      <div class="anam-label">the dictator sees this</div>
    </div>
    <div class="anam-branch">
      <div class="anam-arrow">&darr;</div>
      <div class="anam-node anam-node--hidden">Anamorphic decryption<br><small>double key</small></div>
      <div class="anam-arrow">&darr;</div>
      <div class="anam-node anam-node--hidden">Hidden message&nbsp;<em>M*</em></div>
      <div class="anam-label">only the receiver sees this</div>
    </div>
  </div>
</div>

The remarkable part: even an observer who **controls the encryption system and holds every normal secret key** cannot tell whether a hidden message is present.

## The Challenge: Communicating Under Complete Surveillance

Anamorphic cryptography is usually framed around a worst-case adversary — a *dictator* — who supervises all communication. Crucially, this adversary is far stronger than a typical eavesdropper. They may know:

- the public keys,
- the **normal secret keys**,
- every ciphertext on the wire,
- and the full transcript of communication.

Against an adversary this powerful, simply encrypting a hidden message is hopeless: they can decrypt anything encrypted under the keys they hold. So the goal shifts. It is no longer about confidentiality of content — it is about deniability of **existence**.

<div class="anam-callout" markdown="1">
**The challenge is not breaking encryption. The challenge is hiding the existence of communication.**
</div>

A regular ciphertext and an anamorphic ciphertext must look *identically distributed* to the dictator. The hidden channel has to live inside structure the dictator already expects to see.

## From One Ciphertext to Many

Earlier anamorphic schemes often tried to stuff both meanings into a **single** ciphertext, which forces strong and sometimes unnatural assumptions on the underlying scheme. Our key design move is different and surprisingly freeing:

> Don't overload one ciphertext. **Spread** the hidden information across several ordinary ciphertexts.

We call this a $\mu$-**message anamorphic extension**: the sender produces $\mu$ regular ciphertexts that, taken together, secretly encode one anamorphic message $\amsg$. To the dictator they are just $\mu$ normal encryptions of $\mu$ normal messages.

<div class="anam-callout" markdown="1">
**Syntax (informal).** A $\mu$-message asymmetric anamorphic encryption extension attaches three algorithms to an ordinary PKE scheme:

- $(\apk,\ask,\dk,\tk) \samp \aGen(1^{\secp})$ — generate the usual key pair **plus** an encryption *double key* $\dk$ and a decryption *double key* $\tk$.
- $\actvec \samp \aenc(\apk,\dk,\msgvec,\amsg)$ — encrypt a vector of $\mu$ regular messages $\msgvec$ together with one hidden message $\amsg$, producing $\mu$ ciphertexts.
- $\amsg \samp \adec(\tk,\actvec)$ — recover the hidden message from those ciphertexts using $\tk$.

Without $\tk$ and $\dk$, the dictator sees nothing but ordinary ciphertexts.
</div>

<div class="anam-twocol">
  <div class="anam-panel">
    <h4>What the dictator sees</h4>
    $\ct_1 \to \msg_1$<br>
    $\ct_2 \to \msg_2$<br>
    $\;\vdots$<br>
    $\ct_\mu \to \msg_\mu$<br>
    <p style="margin:.6rem 0 0;opacity:.7;font-size:.85rem">$\mu$ independent, ordinary ciphertexts.</p>
  </div>
  <div class="anam-panel">
    <h4>What the receiver sees</h4>
    $(\ct_1,\ldots,\ct_\mu) \;\to\; \amsg$
    <p style="margin:.6rem 0 0;opacity:.7;font-size:.85rem">The same ciphertexts jointly reveal one hidden message.</p>
  </div>
</div>

The art is in *how* the hidden message is woven into the ciphertexts so that (a) each one still decrypts normally, and (b) the joint structure is invisible without the double key.

## Anamorphic ElGamal: Turning Randomness into a Hidden Channel

ElGamal is the cleanest place to see the trick. A ciphertext is

$$
\ct_1 = (r_1, c_1) = \big(g^{\kappa},\; \pk^{\kappa}\cdot \msg\big),
$$

and decryption recovers $\msg = c_1 \cdot r_1^{-\sk}$, where $\pk = g^{\sk}$.

The crucial observation: ElGamal is **randomized**, and that randomness $\kappa$ looks uniform. Random-looking components are exactly where a hidden signal can hide in plain sight.

In the two-ciphertext extension, the first ciphertext is a perfectly ordinary encryption of $\msg_1$. For the **second** ciphertext $\ct_2 = (r_2, c_2)$, instead of drawing fresh randomness $\kappa_2$ uniformly, we *implicitly* program it:

$$
\kappa_2 \seteq \alpha\,\kappa + \amsg,\qquad
r_2 = g^{\alpha\kappa+\amsg},\qquad
c_2 = g^{\sk(\alpha\kappa+\amsg)}\cdot \msg_2 .
$$

Here the double keys are $\tk=\alpha$ and $\dk=(g^{\alpha}, g^{\alpha \sk})$. The receiver recovers the hidden message as a discrete logarithm,

$$
\amsg = \log\!\big(r_2 \cdot r_1^{-\tk}\big),
$$

which is efficient because the anamorphic message space is a polynomially bounded subset of $\Z_q$. Note that, $(r_1, r_2)$ forms an ElGamal ciphertext encrypting $\amsg$. 

To the dictator, $\actvec = (\ct_1,\ct_2)$ are just two ordinary ElGamal ciphertexts. They never hold $(\tk,\dk)$, so they are completely oblivious to $\amsg$. The hidden channel rides entirely on randomness the dictator already expects to be random.

## Anamorphic Dual-Regev: Hidden Signals in Post-Quantum Encryption

Can the same idea survive in a **post-quantum** setting? Dual-Regev encryption is built on LWE, so its "randomness" is noisy and algebraic rather than a clean exponent — which makes hiding a signal much more delicate.

A Dual-Regev ciphertext for message $\msg$ has the shape

$$
(\rrr^{\top}, c) \seteq \big(\sss^{\top}\AAA + \ee^{\top},\;\; \sss^{\top}\bbb + y + \msg\cdot\floor{q/2} \bmod q\big),
$$

with public key $\pk \seteq (\AAA, \bbb = \AAA\kkk)$ and secret key $\sk \seteq \kkk \samp \lbrace 0,1 \rbrace^m$.

The naive ElGamal-style move — biasing the secret $\sss_2$ so that $\amsg$ sits inside $\rrr_2^{\top}=\sss_2^{\top}\AAA+\ee_2^{\top}$ — **fails**, because there is no way to extract $\sss_2$ back out of $\rrr_2$. So we need a different embedding.

The idea: introduce a second "double" public key $\wh{\bbb} = \AAA\,\wh{\kkk}$, with $\wh{\kkk}\samp\lbrace 0,1 \rbrace^m$ as the decryption double key $\tk$. We then **shape** the randomness of the second ciphertext so that the *first coordinate* of $\rrr_2^{\top}$ becomes a complete little Dual-Regev encryption of $\amsg$ under the double key. Concretely, sample $\sss_2 = [x_1,\dots,x_n]$ with $x_1,\dots,x_{n-1}$ uniform and the last entry chosen as

$$
x_n = \Big(-\textstyle\sum_{i=1}^{n-1} x_i\,a_i \;+\; \textcolor{blue}{\sss_1^{\top}\wh{\bbb} + \wh{y} + \amsg\cdot\floor{q/2}} \;-\; e_{2,1}\Big)\cdot a_n^{-1} \bmod q,
$$

where $\mathbf{a}=(a_1,\dots,a_n)$ is the first column of $\AAA$. This forces the first entry of $\rrr_2^{\top}$ to equal exactly

$$
\textcolor{blue}{\wh{c} = \sss_1^{\top}\wh{\bbb} + \wh{y} + \amsg\cdot\floor{q/2} \bmod q},
$$

so the receiver can read off the regular Dual-Regev ciphertext $(\rrr_1^{\top}, \wh{c})$ and decrypt $\amsg$ with $\tk = \wh{\kkk}$. Under the $\dlwe$ assumption, $\sss_1^{\top}\wh{\bbb}+\wh{y}$ is uniform, so $x_n$ is uniform in the dictator's view — and $\actvec = (\ct_1,\ct_2)$ remain two independent, ordinary Dual-Regev ciphertexts.

<div class="anam-callout" markdown="1">
This demonstrates **anamorphic communication from post-quantum primitives** — the hidden channel coexists with standard LWE-based encryption and without using any lattice tools (e.g., pre-image sampling or trapdoor matrices).
</div>

## Anamorphic Signatures

Encryption is not the only randomized primitive. **Signatures** carry randomness too — which raises a natural question:

> Can a signature authenticate a message while *secretly carrying another one*$~$?

An anamorphic signature should: verify normally under the ordinary verification key, hide an extra message inside, and allow only a designated receiver to extract it. Note the asymmetry we introduce here: the sender signs, and a *separate* receiver — not the verifier — pulls out the hidden message.

<div class="anam-callout" markdown="1">
**Syntax (informal).** A $\mu$-message asymmetric anamorphic *signature* extension of a signature scheme uses:

- $(\ask,\avk,\td) \samp \aGenS(\gp)$ — the sender's signing/verification keys plus secret trapdoor $\td$.
- $(\dk,\tk) \samp \aGenR(\gp)$ — the receiver's encryption/decryption double keys.
- $\asigvec \samp \aSig(\ask,\td,\dk,\msgvec,\amsg)$ — sign $\mu$ messages while embedding the hidden $\amsg$.
- $\amsg \samp \aDecSig(\tk,\avk,\asigvec,\msgvec)$ — the receiver extracts $\amsg$ from the signatures.
</div>

## Anamorphic Waters Signatures

Waters signatures are a natural target because they are full of group elements with random components — perfect carriers. A Waters hash and signature look like

$$
H(\msg)=h_0\prod_{i=1}^{n}h_i^{m_i},\qquad
\sigma = \big(g^{r},\; g^{\alpha\beta}H(\msg)^{r}\big),
$$

for random $r$, secret key $g^{\alpha\beta}$, and public key $(g^{\alpha},g^{\beta},h_0,\ldots,h_n)$.

To carry the hidden payload we use **linear encryption**: public key $(u,v,h)\in\G^3$, and a ciphertext

$$
(c_1,c_2,c_3)=\big(u^{\kappa_1},\,v^{\kappa_2},\,h^{\kappa_1+\kappa_2}\cdot \amsg\big),
$$

which is pseudorandom under the decision-linear ($\dlin$) assumption and decrypts as $\amsg = c_3\,(c_1^{x}c_2^{y})^{-1}$.

The trick: if each user generates its own Waters-hash parameters and therefore knows the discrete logs $a_i$ with $h_i=g^{a_i}$, those logs let the signer **embed one component of a linear-encryption ciphertext into the first element of each of three Waters signatures**. The verifier sees three ordinary signatures; the receiver reassembles the ciphertext and decrypts the hidden message.

## Finding Hidden Messages in a Stream

<style>
.anam-flowrow{display:flex;align-items:center;justify-content:center;gap:.55rem;flex-wrap:wrap;margin:1.3rem 0}
.anam-chain{display:flex;gap:.4rem;justify-content:center;flex-wrap:wrap;align-items:flex-end;margin:.6rem 0 .25rem}
.anam-ccell{min-width:3rem;padding:.5rem .3rem;text-align:center;border:1.5px solid rgba(128,128,128,.45);border-radius:7px;font-family:monospace;font-size:.8rem;font-weight:600;background:rgba(128,128,128,.05)}
.anam-ccell sub{font-weight:400;opacity:.85}
.anam-ccell--mark{border-color:var(--global-theme-color,#0076df);color:var(--global-theme-color,#0076df);border-width:2px;background:rgba(128,128,128,.12)}
.anam-ccell--hid{border-style:dashed;border-color:var(--global-theme-color,#0076df)}
.anam-brace{text-align:center;font-size:.78rem;opacity:.75;font-style:italic;margin-top:.15rem}
</style>

Once the hidden payload is spread across $\mu$ consecutive ciphertexts, the designated receiver faces a new problem: in a long stream of ordinary-looking ciphertexts, *which* short run actually carries the anamorphic message? The brute-force answer — anamorphically trial-decrypting every window of $\mu$ consecutive ciphertexts — is wasteful. **SCAN** (the *Stream Ciphertext Anamorphism Notifier*) lets the receiver jump straight to the right spot instead.

The problem: a receiver watches a long stream and must figure out *where* the hidden channel switches on.

$$
C_1\; C_2\; C_3\; C_4\; C_5 \;\ldots
$$

SCAN solves this with a shared notification key and a pseudorandom function that **marks** the onset of anamorphic transmission — visible only to someone holding the key.

<div class="anam-fig">
  <div style="opacity:.7;font-size:.85rem;margin-bottom:.3rem">Normal stream</div>
  <div class="anam-stream">
    <div class="anam-cell">C</div><div class="anam-cell">C</div><div class="anam-cell">C</div>
    <div class="anam-cell">C</div><div class="anam-cell">C</div><div class="anam-cell">C</div>
  </div>
  <div style="opacity:.7;font-size:.85rem;margin:.6rem 0 .3rem">Stream carrying a hidden signal</div>
  <div class="anam-stream">
    <div class="anam-cell">C</div><div class="anam-cell">C</div>
    <div class="anam-cell anam-cell--sig">&#9733;</div>
    <div class="anam-cell anam-cell--hid">H</div><div class="anam-cell anam-cell--hid">H</div>
    <div class="anam-cell">C</div>
  </div>
  <div class="anam-label">the receiver detects &#9733; and reads the following hidden ciphertexts</div>
</div>

<div class="anam-callout" markdown="1">
**Syntax (informal).** A SCAN with stream length $\stp$ wraps a $\mu$-message extension in a sender–receiver protocol:

- $\nk \samp \nGen(1^{\secp})$ — set up a notification key for signaling.
- $\ctvec \samp \Sen(\apk,\msgvec)$ — the *normal* sender outputs $\stp$ ordinary ciphertexts.
- $\actvec \samp \aSen(\apk,\dk,\nk,\msgvec,(s,\amsg))$ — the *anamorphic* sender additionally embeds a signal $s$ marking where the hidden message $\amsg$ begins.
- $(\msgvec,\anm) \samp \Rec(\apk,\tk,\nk,\ctvec)$ — the receiver scans the stream and recovers the hidden payload.
</div>


**It leans on one realistic assumption: in-order delivery.** Connection-oriented transports — TCP, QUIC, or the TLS record layer — guarantee reliable, in-order delivery, so against a *passive* dictator the receiver sees ciphertexts in exactly the order the sender produced them. Because the $\mu$ anamorphic ciphertexts therefore arrive back-to-back, and $\mu$ is a small constant the receiver already knows, it suffices to covertly mark just the **start** of the block.

**The marker is a PRF-chained ciphertext.** Sender and receiver share a PRF key $K$ (which can simply live inside the receiver's double key $\mathsf{dk}$). To announce that an anamorphic block $(\mathsf{ct}\_{k+1}, \ldots, \mathsf{ct}\_{k+\mu}) \text{ begins at position } k+1$, the sender does *not* draw fresh randomness for the preceding ciphertext $\mathsf{ct}_k$. Instead it derives that randomness from the **previous** ciphertext through the PRF:

$$
r_k \leftarrow \mathsf{PRF}_K(\mathsf{ct}_{k-1}), \qquad
\mathsf{ct}_k = \mathsf{Enc}(\mathsf{pk},\, \mathsf{msg}_k;\, r_k).
$$

<div class="anam-flowrow">
  <div class="anam-node">ct<sub>k-1</sub><br><small>previous ciphertext</small></div>
  <div class="anam-arrow">&rarr;</div>
  <div class="anam-node anam-node--accent">PRF<sub>K</sub></div>
  <div class="anam-arrow">&rarr;</div>
  <div class="anam-node">r<sub>k</sub><br><small>randomness</small></div>
  <div class="anam-arrow">&rarr;</div>
  <div class="anam-node anam-node--hidden">ct<sub>k</sub> = marker<br><small>Enc(pk, msg<sub>k</sub>; r<sub>k</sub>)</small></div>
</div>

So $\mathsf{ct}_k$ is a perfectly valid encryption of an ordinary message $\mathsf{msg}_k$ — it just has randomness secretly tied to its predecessor.

**Detection is a cheap scan.** As ciphertexts arrive, the receiver tests each one for the tell-tale chaining:

$$
\mathsf{ct}_i \;\stackrel{?}{=}\; \mathsf{Enc}\big(\mathsf{pk},\, \mathsf{msg}_i;\, \mathsf{PRF}_K(\mathsf{ct}_{i-1})\big).
$$

For an ordinary ciphertext (fresh randomness) this fails; at the marker $i = k$ it matches. The receiver then knows the next $\mu$ ciphertexts are the anamorphic block — and decodes only those.

<div class="anam-fig">
  <div class="anam-chain">
    <div class="anam-ccell">ct<sub>k-1</sub></div>
    <div class="anam-ccell anam-ccell--mark">ct<sub>k</sub> &#9733;</div>
    <div class="anam-ccell anam-ccell--hid">ct<sub>k+1</sub></div>
    <div class="anam-ccell anam-ccell--hid">ct<sub>k+2</sub></div>
    <div class="anam-ccell anam-ccell--hid">&#8230;</div>
    <div class="anam-ccell anam-ccell--hid">ct<sub>k+&mu;</sub></div>
    <div class="anam-ccell">ct<sub>k+&mu;+1</sub></div>
  </div>
  <div class="anam-brace">&#9733; marker (PRF-chained) &nbsp;&middot;&nbsp; dashed = the &mu; anamorphic ciphertexts &nbsp;&middot;&nbsp; solid = ordinary traffic</div>
</div>

**Why the dictator stays blind.** Without $K$, the PRF output is indistinguishable from uniform randomness — so the marker ciphertext looks exactly like any other freshly randomized encryption. The signal is visible only to someone holding the key.

**Why it's fast.** Each ciphertext costs the receiver only one PRF evaluation, one re-encryption, and a comparison — no anamorphic trial-decryption of every $\mu$-window. The hidden message is recovered in essentially a single pass over the stream.



## SSAN: Signaling for Signatures

The streaming idea ports directly to signatures as **SSAN — the Stream Signature Anamorphism Notifier**. It is the SCAN concept applied to a stream of signatures: the receiver can efficiently *identify which signatures are anamorphic* and extract their hidden contents, while any observer sees nothing but a normal, valid signature stream.

## Why This Matters

Anamorphic cryptography reframes what "secure communication" even means. Its applications point toward settings where the *act* of communicating is itself dangerous:

- privacy-preserving communication under surveillance,
- censorship-resistant systems,
- covert authentication,
- and resilient infrastructure for high-risk environments.

The shift in goal is the whole story:

<div class="anam-fig">
  <div class="anam-node">Traditional cryptography</div>
  <div class="anam-arrow">&darr;</div>
  <div class="anam-node">ciphertext &rarr; protected message</div>
  <div style="height:1rem"></div>
  <div class="anam-node anam-node--accent">Anamorphic cryptography</div>
  <div class="anam-split">
    <div class="anam-branch">
      <div class="anam-arrow">&darr;</div>
      <div class="anam-node">public meaning</div>
    </div>
    <div class="anam-branch">
      <div class="anam-arrow">&darr;</div>
      <div class="anam-node anam-node--hidden">hidden meaning</div>
    </div>
  </div>
</div>

<div class="anam-callout" markdown="1">
**Anamorphic cryptography expands the goal of security: protecting not only *what* is communicated, but *whether* communication exists at all.**
</div>

## Our Constructions at a Glance

We instantiate the multi-message recipe across a range of standard primitives. Here $\mu$ is the number of regular ciphertexts (or signatures) needed to carry one hidden message; the anamorphic message space is **restricted** (polynomial-sized) or **unrestricted** (exponential-sized).

**Anamorphic encryption (AAE) and identity-based anamorphic encryption (IBAE)**

<table class="anam-table">
  <thead>
    <tr><th>Setting</th><th>Base scheme</th><th>&mu; = #ct</th><th>Hidden msg space</th><th>Security</th></tr>
  </thead>
  <tbody>
    <tr><td>AAE</td><td>ElGamal</td><td>2</td><td>restricted</td><td>IND-CPA + sIND-CPA</td></tr>
    <tr><td>AAE</td><td>Dual-Regev</td><td>2</td><td>restricted</td><td>IND-CPA + sIND-CPA</td></tr>
    <tr><td>AAE</td><td>FO–Hashed-ElGamal</td><td>4</td><td>unrestricted</td><td>sIND-RCCA</td></tr>
    <tr><td>AAE</td><td>FO–Hashed-ElGamal</td><td>7</td><td>unrestricted</td><td>sIND-CCA</td></tr>
    <tr><td>IBAE</td><td>Boneh–Franklin</td><td>2</td><td>restricted</td><td>IND-CPA + sIND-CPA</td></tr>
    <tr><td>IBAE</td><td>FO–Boneh–Franklin</td><td>4</td><td>unrestricted</td><td>sIND-RCCA</td></tr>
  </tbody>
</table>

**Anamorphic signatures (AAS)**

<table class="anam-table">
  <thead>
    <tr><th>Encryption used</th><th>Signature</th><th>&mu; = #&sigma;</th><th>Hidden msg space</th><th>Security</th></tr>
  </thead>
  <tbody>
    <tr><td>Linear encryption</td><td>Waters</td><td>3</td><td>unrestricted</td><td>IND-CPA</td></tr>
    <tr><td>ElGamal</td><td>BBS</td><td>4</td><td>unrestricted</td><td>IND-CPA</td></tr>
    <tr><td>FO–ElGamal</td><td>BBS</td><td>5</td><td>unrestricted</td><td>sIND-RCCA + IND-CCA</td></tr>
  </tbody>
</table>

The throughline: starting from primitives close to what people **actually deploy**, a handful of ordinary ciphertexts or signatures is enough to open a hidden, dictator-proof channel — in both the classical and post-quantum worlds.

<div class="anam-callout" markdown="1">
Want the formal definitions, the CCA-secure variants, and full security proofs? They're in the paper: [Cryptology ePrint Archive 2025/370](https://eprint.iacr.org/2025/370).
</div>
