---
layout: page
permalink: /publications/
title: publications
description: publications by categories in reversed chronological order.
nav: true
nav_order: 2
---

<div style="margin: 0 0 1.5rem; font-size: 0.85rem;">
  <span style="display:inline-block;width:11px;height:11px;border-radius:2px;background:#34507a;vertical-align:middle;margin-right:5px;"></span>Conference
  &nbsp;&nbsp;
  <span style="display:inline-block;width:11px;height:11px;border-radius:2px;background:#D35400;vertical-align:middle;margin-right:5px;"></span>Journal
  &nbsp;&nbsp;
  <span style="display:inline-block;width:11px;height:11px;border-radius:2px;background:#9a6d2f;vertical-align:middle;margin-right:5px;"></span>Patent
  &nbsp;&nbsp;
  <span style="display:inline-block;width:11px;height:11px;border-radius:2px;background:#8a8f98;vertical-align:middle;margin-right:5px;"></span>Preprint
</div>

<div id="pub-counts" style="margin: 0 0 1.25rem; font-size: 0.9rem; font-weight: 600;"></div>

<script>
document.addEventListener("DOMContentLoaded", function () {
  var conf = ["CRYPTO","EUROCRYPT","ASIACRYPT","PKC","EuroS&P","ProvSec","LATINCRYPT","ACISP","CANS"];
  var journal = ["JoC","CiC","DCC","TCS"];
  var patent = ["Patent"];
  var preprint = ["ePrint"];
  var c = { Conferences: 0, Journals: 0, Patents: 0, Preprints: 0 };
  document.querySelectorAll("ol.bibliography abbr").forEach(function (el) {
    var t = el.textContent.trim();
    if (conf.indexOf(t) > -1) c.Conferences++;
    else if (journal.indexOf(t) > -1) c.Journals++;
    else if (patent.indexOf(t) > -1) c.Patents++;
    else if (preprint.indexOf(t) > -1) c.Preprints++;
  });
  var parts = ["Conferences: " + c.Conferences, "Journals: " + c.Journals, "Patents: " + c.Patents];
  if (c.Preprints) parts.push("Preprints: " + c.Preprints);
  document.getElementById("pub-counts").textContent = parts.join("   ·   ");
});
</script>

<!-- _pages/publications.md -->

<!-- Bibsearch Feature -->

{% include bib_search.liquid %}

<div class="publications">

{% bibliography %}

</div>
