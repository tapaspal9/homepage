---
layout: post
title: Research visit to the University of Auckland
date: 2019-06-24 16:00:00+1200
description: A doctoral research visit to Prof. Steven Galbraith's group, and a seminar on the road from functional encryption to indistinguishability obfuscation.
inline: false
related_posts: false
---

In June 2019, during my doctoral studies at IIT Kharagpur, I had the pleasure of visiting the [University of Auckland](https://www.auckland.ac.nz) in New Zealand, hosted by Prof. Steven D. Galbraith. Auckland has a long and distinguished tradition in the mathematical foundations of public-key cryptography — elliptic curves, isogenies, and the hard problems that so much of modern security quietly rests upon — and it was a wonderful setting in which to step back from the day-to-day of a single result and think about the larger shape of the field.

While there, I gave a research seminar titled **"From Functional Encryption to Indistinguishability Obfuscation."** The talk followed one of the most striking threads in modern cryptography: how functional encryption — schemes that let a key holder learn a *specific function* of encrypted data and nothing more — turns out to be intimately connected to [indistinguishability obfuscation](https://en.wikipedia.org/wiki/Indistinguishability_obfuscation), a primitive powerful enough to bring an enormous range of cryptographic goals within reach of a single assumption.

---

#### What the talk covered

<ul>
    <li>The definitional landscape of functional encryption, and why properties like collusion-resistance and function-hiding are what make it interesting.</li>
    <li>Compactness as the crucial ingredient: why <em>succinct</em> functional encryption is so much harder — and so much more valuable — than its plain counterpart.</li>
    <li>The bootstrapping route: how compact functional encryption for even simple function classes can be amplified, step by step, toward full obfuscation.</li>
    <li>The open questions that remained at the time around assumptions, efficiency, and what a "practical" path might look like.</li>
</ul>

What I enjoyed most were the conversations around the talk; here is the [slides](https://drive.google.com/file/d/1V1iPobwKTt_0PVGfagk7jaajEwb3r7vu/view?usp=sharing). Explaining a line of work to mathematicians who come at cryptography from the number-theoretic side forces a useful kind of honesty — you cannot hide behind notation, and the questions tend to land exactly on the assumptions you were hoping no one would ask about.

---

> Mathematics is the art of giving the same name to different things. ([more about it](https://www.nieuwarchief.nl/serie5/pdf/naw5-2012-13-3-154.pdf))
>
> — Henri Poincaré

That sentiment is, in a way, the whole story of the talk: functional encryption and obfuscation look like very different objects, until you find the right vantage point from which they are almost the same thing. I'm grateful to Prof. Galbraith and the Auckland mathematics community for the warm hospitality and for a visit that genuinely sharpened how I think about my own research.
