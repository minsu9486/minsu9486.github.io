---
title: "AI for Hide-and-Seek"
permalink: /portfolio/ai-game-tilemap/
date: 2020-10-10
excerpt: "A 2D tech demo that shows the core AI of hide-and-seek using terrain analysis, pathfinding, agent awareness, etc."
header:
  image: /assets/images/aiGameHnS_main_wide.png
  teaser: /assets/images/aiGameHnS_teaser.png
company: "Academic Project"
platform: "Windows PC"
skills: "C++"
role: "Game AI Engineer"
responsibilities: "Terrain Analysis, Pathfinding"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Features
 - A* algorithm with rubberband and Catmull-Rom spline
 - Hide-and-seek with visibility, field of view, and propagation

## Hide and Seek
<figure class="half">
	<img src="/assets/images/aiGameHnS_hideAndSeek_1.gif">
	<img src="/assets/images/aiGameHnS_hideAndSeek_2.gif">
</figure>

 - Field of view is applied for the red agent as 180 degrees with a circular vision around it.
 - The red propagation means suspicious area which is spread from the last spotted tile and is not searched yet so that the AI can keep search around there with priority.

### A* Algorithm

#### Huristic
<figure class="half">
	<img src="/assets/images/aiGameHnS_huristic_double.gif">
	<img src="/assets/images/aiGameHnS_huristic_zero.gif">
  <figcaption>1) A* with huristic &emsp;&emsp;&emsp;&emsp; 2) A* with no huristic</figcaption>
</figure>

#### Smooth Moving
<figure class="half">
	<img src="/assets/images/aiGameHnS_aStar_naive.gif">
	<img src="/assets/images/aiGameHnS_aStar_rubbandNSpline.gif">
  <figcaption>1) A naive A* &emsp;&emsp;&emsp;&emsp; 2) A* with rubberband and Catmull-Rom spline </figcaption>
</figure>

## References
 - [Game AI Pro 3](http://www.gameaipro.com/)