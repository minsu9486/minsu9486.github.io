---
title: "3D Spatial Algorithms"
permalink: /portfolio/spatial-algorithms/
date: 2020-04-01
excerpt: "Bounding volume hierarchies and spatial algorithms for geometric operations"
header:
  image: /assets/images/spatialAlg_main_wide.png
  caption: "A sphere with second depth of octree"
  teaser: /assets/images/spatialAlg_teaser.png
company: "Academic project"
engine: "Custom engine based on GLFW"
platform: "Windows PC"
skills: "C++, OpenGL, GLSL, Linear algebra"
role: "Graphics engineer"
responsibilities: "Implementing robust and flexible spatial algorithms"
---

<style>
.markdown_list ul { margin-bottom:10px; list-style-position: outside; }
.hover_img { position:relative; }
.hover_img span { position:absolute; display:none; }
.hover_img:hover span { display:block; width:25vw; min-width:250px;}
</style>

| |
| **company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

### Features
 - Hybrid rendering: Deferred + forward shading
 - Bounding volume hierarchies
 - Spatial partitioning: Octree

#### Hybrid Rendering
<figure>
  <img src="/assets/images/spatialAlg_banny_def_all.png">
  <figcaption>It defers most of the heavy rendering (like lighting) to a later stage. The final image comes from G-buffer which is a combined data of position, nomals, albedo, and specular. In this project, final objects are rendered in deferred shading, whereas debug information such as position of lights, normal vectors, and bounding volumes are rendered in forward shading.</figcaption>
</figure>

#### Bounding Volume Hierarchies
<figure class="third">
	<img src="/assets/images/spatialAlg_banny_TD_AABB_2.png">
	<img src="/assets/images/spatialAlg_banny_TD_AABB_4.png">
	<img src="/assets/images/spatialAlg_banny_TD_AABB_all.png">
  <figcaption>A top-down hierarchy using axis-aligned bounding boxs. 1) Second depth. 2) Forth depth. 3) Showing all depths.</figcaption>
</figure>

<figure class="half">
	<img src="/assets/images/spatialAlg_banny_BV_OBB.png">
	<img src="/assets/images/spatialAlg_banny_TD_Sphe_4.png">
	<figcaption>1) A oriented bounding box 2) Forth depth of a top-down hierarchy using bounding spheres.</figcaption>
</figure>

#### Octree
<figure class="third">
	<img src="/assets/images/spatialAlg_banny_oct_1.png">
	<img src="/assets/images/spatialAlg_banny_oct_2.png">
	<img src="/assets/images/spatialAlg_banny_oct_3.png">
  <figcaption>Octree is a tree data structure in which each internal node has exactly eight children. It is used to partition a three-dimensional space by recursively subdividing it into eight octants. 1) First depth. 2) Second depth. 3) Third depth.</figcaption>
</figure>

### Sorce Code
 - [HybridRendering.cpp](/scripts/sa-hybrid-rendering.cpp/)
 - [Octree.cpp](/scripts/sa-octree.cpp/)

### Used External Libraries
 - GLEW: The OpenGL Extension Wrangler Library
 - GLFW: An OpenGL library
 - GLM: C++ mathematics library for OpenGL Shading Language
 - PFD: A free C++11 file dialog library
 - STB: Public domain libraries for C/C++
 - ImGui: Bloat-free Immediate Mode Graphical User interface for C++
 - Assimp: A library to import and export various 3d-model-formats

### References
 - learnopengl.com
 - Real-Time Collision Detection