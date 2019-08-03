---
title: "Adaptation"
permalink: /adaptation/
excerpt: "A 2D survive game which two players should cooperate together and evolve their body parts."
header:
  image: /assets/images/adap_main_wide.png
  teaser: /assets/images/adap_char.png
category: "Student project"
engine: "Full-custom engine"
platform: "Windows PC"
skills: "C++"
role: "Gameplay & tool engineer"
responsibilities: "Terrain & level design"
---

<style>
.hover_img b { font-size:1.25em; }
.hover_img a { position:relative; }
.hover_img a span { position:absolute; display:none; }
.hover_img a:hover span { display:block; width: 25vw; min-width: 250px;}
</style>


| |
| **Category**	|{{page.category}}								|||**Engine**			|{{page.engine}}
| **Platform**	|{{page.platform}}								|||**Skills**			|{{page.skills}}	
| |
| **Role**		|<span style="color:red">{{page.role}}</span>	|||**Responsibilities**	|<span style="color:red">{{page.responsibilities}}</span>

### Assigned Tasks
<div class="hover_img">
  <b>&nbsp;&nbsp;&nbsp;•&nbsp;&nbsp;</b>A tile-based platform which can be destroyed and has no size limits<br>
  <b>&nbsp;&nbsp;&nbsp;•&nbsp;&nbsp;</b>A randomized terrain generator (cave, floating land, flat, mountain, valley)<br>
  <b>&nbsp;&nbsp;&nbsp;•&nbsp;&nbsp;</b>Organizing all object data in each tile for collision detection and interaction<br>
  <b>&nbsp;&nbsp;&nbsp;•&nbsp;&nbsp;</b>A external 
  <a href="/blanc-animator">2D skeletal animation tool<span><img src="/assets/images/anitool_run_play.gif" /></span></a>
  for the characters' motion
</div>

### Description
<figure class="third">
	<img src="/assets/images/adap_ingame_1.jpg">
	<img src="/assets/images/adap_ingame_2.PNG">
	<img src="/assets/images/adap_ingame_3.jpg">
	<figcaption>It is basically consisted of dots(joints) and lines(bones) with several data. This tool supports a convenient function which is child bones automatically follow their parent joint. The exported format is INI file.</figcaption>
</figure>

<figure>
	<img src="/assets/images/adap_tech.png">
	<figcaption>The maximum frame is eight, and each frame is controlled by different time span. Using these elements, objects can express various and complex motions in the game.</figcaption>
</figure>
