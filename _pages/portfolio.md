---
title: Portfolio
permalink: /portfolio/
layout: single
header:
  overlay_color: "#000"
author_profile: true
sort_by: "date"
sort_order: reverse
toc: true
toc_sticky: true
---

<style>
.align-center { width: 40vw; max-width: 600px; min-width: 300px; }
</style>

{% assign professionals = site.portfolio | where: "category", "professional" | sort: "date" | reverse %}
{% assign games        = site.portfolio | where: "category", "games"        | sort: "date" | reverse %}
{% assign graphics     = site.portfolio | where: "category", "graphics"     | sort: "date" | reverse %}
{% assign tools        = site.portfolio | where: "category", "tools"        | sort: "date" | reverse %}

<h1 id="professional-projects" style="text-align: center;">Professional Projects</h1>
{% for entry in professionals %}
  <figure class="align-center">
    {% assign bookmark = entry.title | replace: " ", "-" %}
    <h2 id="{{bookmark}}"> {{entry.title}} </h2>
    <a href="{{entry.permalink}}">
      <img src="{{ site.url }}{{ site.baseurl }}{{entry.header.teaser}}" alt="{{entry.title}}">
    </a>
    <figcaption>{{entry.excerpt}}</figcaption>
  </figure>
{%endfor%}

***
<br/><br/>

<h1 id="games-and-interactive" style="text-align: center;">Games &amp; Interactive</h1>
{% for entry in games %}
  <figure class="align-center">
    {% assign bookmark = entry.title | replace: " ", "-" %}
    <h2 id="{{bookmark}}"> {{entry.title}} </h2>
    <a href="{{entry.permalink}}">
      <img src="{{ site.url }}{{ site.baseurl }}{{entry.header.teaser}}" alt="{{entry.title}}">
    </a>
    <figcaption>{{entry.excerpt}}</figcaption>
  </figure>
{%endfor%}

***
<br/><br/>

<h1 id="graphics-and-engines" style="text-align: center;">Graphics &amp; Engines</h1>
{% for entry in graphics %}
  <figure class="align-center">
    {% assign bookmark = entry.title | replace: " ", "-" %}
    <h2 id="{{bookmark}}"> {{entry.title}} </h2>
    <a href="{{entry.permalink}}">
      <img src="{{ site.url }}{{ site.baseurl }}{{entry.header.teaser}}" alt="{{entry.title}}">
    </a>
    <figcaption>{{entry.excerpt}}</figcaption>
  </figure>
{%endfor%}

***
<br/><br/>

<h1 id="tools-and-backend" style="text-align: center;">Tools &amp; Backend</h1>
{% for entry in tools %}
  <figure class="align-center">
    {% assign bookmark = entry.title | replace: " ", "-" %}
    <h2 id="{{bookmark}}"> {{entry.title}} </h2>
    <a href="{{entry.permalink}}">
      <img src="{{ site.url }}{{ site.baseurl }}{{entry.header.teaser}}" alt="{{entry.title}}">
    </a>
    <figcaption>{{entry.excerpt}}</figcaption>
  </figure>
{%endfor%}

***
