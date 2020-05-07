---
title: "Photorealistic Rendering"
permalink: /portfolio/photorealistic-rendering/
date: 2019-11-01
excerpt: "Graphics pipeline for photorealistic rendering in interactive simulations"
header:
  image: /assets/images/pthotoRen_main_wide.png
  caption: "A sphere with nine lights and environment mapping"
  teaser: /assets/images/pthotoRen_dynamicReflec_rabbit.gif
company: "Academic project"
engine: "Custom engine based on GLFW"
platform: "Windows PC"
skills: "C++, OpenGL, GLSL"
role: "Graphics engineer"
responsibilities: "Graphics pipeline for loading and rendering"
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
 - Loading and displaying geometry using OpenGL
 - Implementing phong illumination model
 - Dynamic reflection and refraction with environment mapping

#### Loading & Displaying Geometry
<figure class="third">
	<img src="/assets/images/pthotoRen_1_LDG_objfile.png">
	<img src="/assets/images/pthotoRen_1_LDG_rhino.png">
  <img src="/assets/images/pthotoRen_1_LDG_cube.png">
	<figcaption>Object data is loaded by my custom phaser from a obj file. After it completes to read all data from a obj file, face and vertex normals are calcuated. The method of calculating vertex normal is a smooth fashion which smoothens face vertexes around one vertex.</figcaption>
</figure>


#### Phong Illumination Model
<figure class="third">
	<img src="/assets/images/pthotoRen_2_illum_spot.png">
	<img src="/assets/images/pthotoRen_2_illum_direc.png">
	<img src="/assets/images/pthotoRen_2_illum_point.png">
  <figcaption>Three types of lighting (spotlight, directional light, point light) on a texture-mapped object.</figcaption>
</figure>

<figure>
	<img src="/assets/images/pthotoRen_2_illum_overall.gif">
  <figcaption>Dynamic interaction is viable with the following controllable attributes: shader types (phong lighting, phong shading, blinn shading), the three light types, mapping types (spherical, cylindrical, planar), mapping ways (CPU, GPU), mapping entities (positin, normal), number of lights, etc.</figcaption>
</figure>

#### Dynamic Reflection & Refraction
<figure class="third">
	<img src="/assets/images/pthotoRen_3_env_sphe_comb_air.png">
	<img src="/assets/images/pthotoRen_3_env_sphe_refrac_air.png">
	<img src="/assets/images/pthotoRen_3_env_sphe_refrac_water.png">
  <figcaption>1) Reflection and refraction with Fresnel effect. 2) Refraction with air incidence. 3) Refraction with water incidence.</figcaption>
</figure>

<figure class="half">
	<img src="/assets/images/pthotoRen_3_env_reflec_cub.gif">
	<img src="/assets/images/pthotoRen_3_env_combi_4spheres.gif">
	<figcaption>1) A cup with environment map. 2) A complex object with red, blue, green lights.</figcaption>
</figure>

### Used External Libraries
 - GLEW: The OpenGL Extension Wrangler Library
 - GLFW: An OpenGL library
 - GLM: C++ mathematics library for OpenGL Shading Language
 - PFD: A free C++11 file dialog library
 - STB: Public domain libraries for C/C++
 - ImGui: Bloat-free Immediate Mode Graphical User interface for C++

### References
 - learnopengl.com
 - OpenGL Programming Guide: The Official Guide to Learning OpenGL
 - OpenGL SuperBible: Comprehensive Tutorial and Reference