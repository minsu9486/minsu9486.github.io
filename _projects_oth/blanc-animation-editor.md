---
title: "Blanc Animation Editor"
permalink: /blanc-animator/
excerpt: "A 2D skeletal animation tool for my other team project, Adaptation."
header:
  image: /assets/images/anitool_whole_wide.png
  teaser: /assets/images/anitool_geometry_play.gif
type: "Student project"
platform: "Windows"
skills: "C#, Linear algebra"
role: "Tool engineer"
responsibilities: "Animation tool for [Adaptation](/adaptation)"
---

| |
| **Category**	|{{page.type}}									|||**Engine**			|{{page.engine}}
| **Platform**	|{{page.platform}}								|||**Skills**			|{{page.skills}}	
| |
| **Role**		|<span style="color:red">{{page.role}}</span>	|||**Responsibilities**	|<span style="color:red">{{page.responsibilities}}</span>

### Assigned Tasks
 - Implementing save & load system
 - Implementing natural moving bones which automatically follow their parent
 - ~~Implementing texture preview system~~ (Test-Box in the image)

### Description
<figure class="half">
	<img src="/assets/images/anitool_demo.gif">
	<img src="/assets/images/anitool_exported_info.png">
	<figcaption>It is basically consisted of dots(joints) and lines(bones) with several data. This tool supports a convenient function which is child bones automatically follow their parent joint. The exported format is INI file.</figcaption>
</figure>

<figure class="half">
	<img src="/assets/images/anitool_whole.PNG">
	<img src="/assets/images/anitool_ingame.gif">
	<figcaption>The maximum frame is eight, and each frame is controlled by different time span. Using these elements, objects can express various and complex motions in the game.</figcaption>
</figure>
