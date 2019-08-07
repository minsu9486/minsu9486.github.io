---
title: Portfolio
permalink: /portfolio/
collection: portfolio
sort_by: "date"
entries_layout: grid
sort_order: reverse
classes: wide
---

WILL USE FEATURE_ROW TO ORGANIZE CONTENTS  
https://mmistakes.github.io/minimal-mistakes/splash-page/  
https://github.com/mmistakes/minimal-mistakes/blob/master/docs//_pages/splash-page.md  

### Professional & Other Projects: 
***
{% assign professionals = site.portfolio | where: "professional", true | sort: "date" | reverse %}
{% assign others = site.portfolio | where: "professional", false | sort: "date" | reverse %}

{% for professional in professionals %}
    {{professional.title}}
{%endfor%}

{% for other in others %}
    {{other.title}}
{%endfor%}
***

{% for portfolio in site.portfolio %}
  <a href="{{portfolio.url}}">{{portfolio.title}}</a>
{%endfor%}