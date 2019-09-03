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

{% assign professionals = site.portfolio | where: "professional", true | sort: "date" | reverse %}
{% assign others = site.portfolio | where: "professional", false | sort: "date" | reverse %}

<h1 id="professional-projects" style="text-align: center;">Professional Projects</h1>
{% for professional in professionals %}
  <figure class="align-center">
    {% assign bookmark = professional.title | replace: " ", "-" %}
    <h2 id="{{bookmark}}"> {{professional.title}} </h2>
    <a href="{{professional.permalink}}">
      <img src="{{ site.url }}{{ site.baseurl }}{{professional.header.teaser}}" alt="">
    </a>
    <figcaption>{{professional.excerpt}}</figcaption>
  </figure> 
{%endfor%}

***
<br/><br/>

<h1 id="other-projects" style="text-align: center;">Other Proejcts</h1>
{% for other in others %}
  <figure class="align-center">
    {% assign bookmark = other.title | replace: " ", "-" %}
    <h2 id="{{bookmark}}"> {{other.title}} </h2>
    <a href="{{other.permalink}}">
      <img src="{{ site.url }}{{ site.baseurl }}{{other.header.teaser}}" alt="">
    </a>
    <figcaption>{{other.excerpt}}</figcaption>
  </figure> 
{%endfor%}

***
