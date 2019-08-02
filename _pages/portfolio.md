---
title: "Portfolio"
permalink: /portfolio/
collection1: projects_pro
collection2: projects_oth
entries_layout: grid
classes: wide
---

Professional Projects:
 - ~~LINE: JoJo's Bizarre Adventure - Stardust Shooters~~

Other Projects:
 - Floating Fuzzy
 - Adaptation
 - ~~Blanc Animator~~
 - Don't Stare

### Professional Projects & Other Projects:  
***
<div class="entries-{{ page.entries_layout }}">
  {% include documents-collection.html collection=page.collection1 sort_by=page.sort_by sort_order=page.sort_order type=page.entries_layout %}
</div>

<div class="entries-{{ page.entries_layout }}">
  {% include documents-collection.html collection=page.collection2 sort_by=page.sort_by sort_order=page.sort_order type=page.entries_layout %}
</div>