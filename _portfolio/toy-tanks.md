---
title: "Toy Tanks"
permalink: /portfolio/toy-tanks/
date: 2020-04-15
excerpt: "A 3D shooting game that I worked with five other members. I mainly focused on <b>multi-gamepad input system</b> for four players and <b>object motion</b> based on Bullet Physics."
header:
  image: /assets/images/toyTanks_main_wide.png
  teaser: /assets/images/toyTanks_teaser.gif
company: "Academic Project"
engine: "Custom Engine"
platform: "Windows PC"
skills: "C++, Win32, XInput"
role: "Engine & Gameplay Engineer"
responsibilities: "Multi-gamepad input system & object motion"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Assigned Tasks
 - The engine loop using Win32
 - Multi-gamepad input system for four players
 - Object motion using Bullet Physics

### Multi-Gamepad Input System
 - It supports a maximum of four local gamepads.
 - It is able to dynamically connect and disconnect in the ongoing game. All game objects are corresponding with the current state of each gamepad connection.

### Object Motion
 - Part decoupling is shown for a destructive effect.
 - A tank's parts are respectively moved and rotated by the player's control.

<figure class="half">
	<img src="/assets/images/toyTanks_tank_decoupling.gif">
	<img src="/assets/images/toyTanks_tank_motion.gif">
</figure>

## Used External Libraries
 - WinAPI: Microsoft's core set of application programming interfaces.
 - Bullet: A physics engine for collision detection, soft and rigid body dynamics.
 - FMOD: A proprietary sound effects engine and authoring tool.
 - DirectX: A collection of APIs for handling game programming on Microsoft platforms.
 - ImGui: Bloat-free Immediate Mode Graphical User interface for C++
