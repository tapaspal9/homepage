---
layout: page
title: resource center
permalink: /books/
nav: true  # choose false if show it in subminus
nav_order: 6        # ← keep whatever value your file currently has, delete this if show it in subminus
description: A curated library of texts and lecture notes for research and teaching.
---

<style>
  .lib-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
    gap: 1.6rem;
    margin-top: 1.5rem;
  }
  .lib-card a {
    display: block;
    text-decoration: none;
    color: inherit;
    text-align: center;
  }
  .lib-card img {
    width: 100%;
    aspect-ratio: 2 / 3;
    object-fit: cover;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
    transition: transform 0.15s ease, box-shadow 0.15s ease;
  }
  .lib-card a:hover img {
    transform: translateY(-4px);
    box-shadow: 0 6px 16px rgba(0, 0, 0, 0.22);
  }
  .lib-card h3 { font-size: 1rem; margin: 0.6rem 0 0.2rem; }
  .lib-card p  { font-size: 0.82rem; opacity: 0.7; margin: 0; line-height: 1.35; }
</style>

<div class="lib-grid">
{% assign items = site.books | sort: "importance" %}
{% for book in items %}
  <div class="lib-card">
    <a href="{{ book.url | relative_url }}">
      {% if book.cover %}
        <img src="{{ book.cover | relative_url }}" alt="{{ book.title }} cover" loading="lazy">
      {% endif %}
      <h3>{{ book.title }}</h3>
      {% assign blurb = book.description | default: book.author %}
      {% if blurb %}<p>{{ blurb }}</p>{% endif %}
    </a>
  </div>
{% endfor %}
</div>
