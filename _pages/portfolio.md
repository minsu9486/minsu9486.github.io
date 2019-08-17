---
title: Portfolio
permalink: /portfolio/
layout: splash
collection: portfolio
sort_by: "date"
sort_order: reverse
---

WILL USE FEATURE_ROW TO ORGANIZE CONTENTS  
https://mmistakes.github.io/minimal-mistakes/splash-page/  
https://github.com/mmistakes/minimal-mistakes/blob/master/docs//_pages/splash-page.md  

***
{% assign professionals = site.portfolio | where: "professional", true | sort: "date" | reverse %}
{% assign others = site.portfolio | where: "professional", false | sort: "date" | reverse %}

### Professional Projects: 
{% for professional in professionals %}
    {{professional.title}}
{%endfor%}


### Other Projects: 
{% for other in others %}
    {{other.title}}
{%endfor%}
***

{% for portfolio in site.portfolio %}
  <a href="{{portfolio.url}}">{{portfolio.title}}</a>
{%endfor%}