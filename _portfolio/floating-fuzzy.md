---
title: "Floating Fuzzy"
permalink: /portfolio/floating-fuzzy/
date: 2017-01-01
excerpt: "A 2D top-down dodge game in Android, where a size-adjustalbe fuzz should avoid other incoming fuzzies."
header:
  image: /assets/images/fuzzy_main_wide.png
  teaser: /assets/images/fuzzy_icon.png
professional: true
company: "Mango Kong"
engine: "Unity"
platform: "Android mobile"
skills: "C#"
role: "Client-side engineer"
responsibilities: "Gameplay, UI, & game design"
toc: true
toc_label: "Table of Contents"
toc_sticky: true
---

| |
| **company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

### Game Description
<figure class="third">
	<img src="/assets/images/fuzzy_tutorial.jpg">
	<img src="/assets/images/fuzzy_ingame_1.jpg">
	<img src="/assets/images/fuzzy_ingame_2.jpg">
	<figcaption>The fuzz with eyes on the top of the screen is the playable character. The fuzz can grow by eating smaller fuzzes or can shrink by rapidly moving right and left. The level of difficulty is adjusted by the player character's size and is increased as time passes.</figcaption>
</figure>

<figure class="half">
	<img src="/assets/images/fuzzy_ingame_dead.jpg">
	<img src="/assets/images/fuzzy_rank.jpg">
	<figcaption>If the player's fuzz is eaten by a bigger one or is pumped into a triangular one, the fuzz will be dead. The score will be recorded in the developer's server with the ranking list as shown above.</figcaption>
</figure>

### Assigned Tasks
 - Implemented a randomized obstacle generator: color, size, speed, and sound pitch
 - Developed an automatic level controller based on a player’s attribute
 - Cooperated in designing the game and the level of difficulty

### Code
 - [EnemyManager.cs](/scripts/ff-enemy-manager.cs/) manages 3 types of objects with randomized attributes.
 - [PlayerMovement.cs](/scripts/ff-player-collision.cs/) controls mobile and PC inputs and related action.
 - [PlayerCollision.cs](/scripts/ff-player-movement.cs/) deals with collision response and each attributes.

### [App Info Archive](https://apkpure.com/floating-fuzzy/com.MangoCompany.FloatingFuzzy)
{: style="text-align: center;"}