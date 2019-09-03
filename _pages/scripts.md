---
title: "Script"
permalink: /scripts/
layout: single
header:
  overlay_color: "#000"
author_profile: true
---

{% assign scripts = site.scripts %}

<h1 style="text-align: center;">Scripts</h1>
{% for script in scripts %}
  <figure class="align-center">
    <a href="{{script.permalink}}">{{script.title}}</a>
  </figure> 
{%endfor%}