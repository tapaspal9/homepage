---
layout: post
title: Research visit to the University of St. Gallen and a talk at Swiss Crypto Day
date: 2023-09-01 10:00:00+0200
description: A research stay in Prof. Katerina Mitrokotsa's Cyber Security Group, and a talk on bringing inner product functional encryption closer to the real world.
inline: false
related_posts: false
---

In the autumn of 2023, I spent September and October as a visiting researcher in the [Cyber Security Group](https://www.unisg.ch/en) at the University of St. Gallen, hosted by Prof. Katerina Mitrokotsa. The visit was part of our Swiss National Science Foundation collaboration on multi-client, attribute-based, unbounded inner product functional encryption — a line of work aimed at making expressive computation over encrypted data both more flexible and closer to something one could actually deploy.


Much of the time was spent at the whiteboard with the group, working through the design of schemes that let several independent clients contribute encrypted inputs while a function key reveals only a specific aggregate — and nothing else. The hardest and most rewarding part of such a visit is not the writing but the arguing: tracing exactly where a security proof bends, and where access control and unboundedness can be added without breaking it.

---

While in Switzerland, I also had the chance to present at **Swiss Crypto Day**, held at ETH Zurich, where I gave a talk titled **"Inner Product Functional Encryption for the Real World."** The talk made the case that inner product functional encryption is one of the most deployment-ready corners of functional encryption, and asked what still stands between the elegant theory and practical use:

<ul>
    <li>Why inner products are expressive enough to matter — statistics, weighted sums, and linear models over encrypted data — yet simple enough to build efficiently.</li>
    <li>The multi-client setting, where inputs come from mutually distrusting parties, and why it is the realistic model for most applications.</li>
    <li>Adding access control and removing artificial bounds on the data, so a scheme does not need to know the world's shape in advance.</li>
    <li>The practical gaps that remain — key sizes, assumptions, and the engineering distance between a proof and a product.</li>
</ul>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/stgallen-2023.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/swiss-crypto-day-2023.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Left: Hiking Tour: University of St. Gallen. Right: Swiss Crypto Day at ETH Zurich.
</div>

There is a well-worn saying that in theory, theory and practice are the same, but in practice, they are not. That gap is exactly what makes inner product functional encryption such a satisfying thing to work on. My thanks to Prof. Mitrokotsa and the St. Gallen group for the generous hospitality, and to the Swiss Crypto Day organizers for a lively audience and sharp questions.
