---
title: "3D Spatial Algorithms"
permalink: /portfolio/spatial-algorithms/
date: 2020-04-01
excerpt: "Bounding volume hierarchies and spatial algorithms for geometric operations"
header:
  image: /assets/images/spatialAlg_main_wide.png
  caption: "A sphere with second depth of octree"
  teaser: /assets/images/spatialAlg_teaser.png
company: "Academic Project"
engine: "Custom engine based on GLFW"
platform: "Windows PC"
skills: "C++, OpenGL, GLSL, Linear Algebra"
role: "Graphics Engineer"
responsibilities: "Implementing robust and flexible spatial algorithms"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Features
 - Bounding volume hierarchies
 - Spatial partitioning: Octree

### Bounding Volume Hierarchies
<figure class="third">
	<img src="/assets/images/spatialAlg_banny_TD_AABB_2.png">
	<img src="/assets/images/spatialAlg_banny_TD_AABB_4.png">
	<img src="/assets/images/spatialAlg_banny_TD_AABB_all.png">
  <figcaption>A top-down hierarchy using axis-aligned bounding boxs. &emsp;&emsp;&emsp;&emsp; 1) Second depth. &emsp;&emsp;&emsp;&emsp; 2) Forth depth. &emsp;&emsp;&emsp;&emsp; 3) Showing all depths.</figcaption>
</figure>

<figure class="half">
	<img src="/assets/images/spatialAlg_banny_BV_OBB.png">
	<img src="/assets/images/spatialAlg_banny_TD_Sphe_4.png">
	<figcaption>1) A oriented bounding box 2) Forth depth of a top-down hierarchy using bounding spheres.</figcaption>
</figure>

### Octree
<figure class="third">
	<img src="/assets/images/spatialAlg_banny_oct_1.png">
	<img src="/assets/images/spatialAlg_banny_oct_2.png">
	<img src="/assets/images/spatialAlg_banny_oct_3.png">
  <figcaption>Octree is a tree data structure in which each internal node has exactly eight children. It is used to partition a three-dimensional space by recursively subdividing it into eight octants. &emsp;&emsp;&emsp;&emsp; 1) First depth. &emsp;&emsp;&emsp;&emsp; 2) Second depth. &emsp;&emsp;&emsp;&emsp; 3) Third depth.</figcaption>
</figure>

## Sorce Code
 - [HybridRendering.cpp](/scripts/sa-hybrid-rendering.cpp/)
 - [Octree.cpp](/scripts/sa-octree.cpp/)

## Used External Libraries
 - GLEW: The OpenGL Extension Wrangler Library
 - GLFW: An OpenGL library
 - GLM: C++ mathematics library for OpenGL Shading Language
 - PFD: A free C++11 file dialog library
 - STB: Public domain libraries for C/C++
 - ImGui: Bloat-free Immediate Mode Graphical User interface for C++
 - Assimp: A library to import and export various 3d-model-formats

## References
 - learnopengl.com
 - Real-Time Collision Detection