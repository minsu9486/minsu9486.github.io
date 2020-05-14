---
title: "Adaptation"
permalink: /portfolio/adaptation/
date: 2015-03-01
excerpt: "It's a 2D survive game project with four other members. I worked on randomized terrain generator and opimaized collision detection."
header:
  image: /assets/images/adap_main_wide.png
  teaser: /assets/images/adap_char.png
company: "Academic project"
engine: "Full-custom engine"
platform: "Windows PC"
skills: "C++"
role: "Gameplay & tool engineer"
responsibilities: "Terrain & level design"
---

<style>
.markdown_list ul { margin-bottom:10px; list-style-position: outside; }
.hover_img { position:relative; }
.hover_img span { position:absolute; display:none; }
.hover_img:hover span { display:block; width:25vw; min-width:250px;}
</style>

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Assigned Tasks 
<div class="markdown_list">
  <ul>
    <li>A tile-based platform which can be destroyed and has no size limits</li>
    <li>A randomized terrain generator (cave, floating land, flat, mountain, valley)</li>
    <li><a href="#tech-description" title="Refer Tech Desc.">Organizing all object data</a> in each tile, where objects are, for efficient collision detection</li>
    <li>
	  An external
      <a class="hover_img" href="/portfolio/blanc-animator" title="Go to Blanc Animation Editor">
        2D skeletal animation tool<span><img src="/assets/images/anitool_run_play.gif" /></span>
      </a>
      for the characters' motion
    </li>
  </ul>
</div>

## Game Description
<figure class="third">
	<img src="/assets/images/adap_ingame_1.jpg">
	<img src="/assets/images/adap_ingame_2.PNG">
	<img src="/assets/images/adap_ingame_3.jpg">
	<figcaption>It's an action based survival game featuring upgrading characters by attaching
multiple unique parts. Two players can control each character on the open-world stage. They should cooperate to survive and collect resources so that the players can attach unique parts with own skills.</figcaption>
</figure>

## Tech Description
<figure>
	<img src="/assets/images/adap_tech.png">
	<figcaption>All moving objects are allocated a 3x3 box which centers around the object itself and has data such as a terrain type or currently which objects are. Therefore, moving objects especially a player character don't need to check other objects which is outside of their 3x3 box. This technique allows to reduce lots of computation time on collision detection and other interaction check.</figcaption>
</figure>