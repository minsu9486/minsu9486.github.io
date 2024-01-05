---
title: "Photorealistic Rendering"
permalink: /portfolio/photorealistic-rendering/
date: 2019-11-01
excerpt: "Graphics pipeline for photorealistic rendering in interactive simulations"
header:
  image: /assets/images/pthotoRen_main_wide.png
  caption: "A sphere with nine lights and environment mapping"
  teaser: /assets/images/pthotoRen_dynamicReflec_rabbit.gif
company: "Academic Project"
engine: "Custom engine based on GLFW"
platform: "Windows PC"
skills: "C++, OpenGL, GLSL"
role: "Graphics Engineer"
responsibilities: "Graphics pipeline for loading and rendering"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Features
 - Loading and displaying geometry using OpenGL
 - Implementing phong illumination model
 - Dynamic reflection and refraction with environment mapping
 - Hybrid rendering: Deferred + forward shading

### Loading & Displaying Geometry
<figure class="third">
	<img src="/assets/images/pthotoRen_1_LDG_objfile.png">
	<img src="/assets/images/pthotoRen_1_LDG_rhino.png">
  <img src="/assets/images/pthotoRen_1_LDG_cube.png">
	<figcaption>Object data is loaded by my custom phaser from a obj file. After it completes to read all data from a obj file, face and vertex normals are calcuated. The method of calculating vertex normal is a smooth fashion which smoothens face vertexes around one vertex.</figcaption>
</figure>


### Phong Illumination Model
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

### Dynamic Reflection & Refraction
<figure class="third">
	<img src="/assets/images/pthotoRen_3_env_sphe_comb_air.png">
	<img src="/assets/images/pthotoRen_3_env_sphe_refrac_air.png">
	<img src="/assets/images/pthotoRen_3_env_sphe_refrac_water.png">
  <figcaption>1) Reflection and refraction with Fresnel effect. &emsp;&emsp;&emsp;&emsp; 2) Refraction with air incidence. &emsp;&emsp;&emsp;&emsp; 3) Refraction with water incidence.</figcaption>
</figure>

<figure class="half">
	<img src="/assets/images/pthotoRen_3_env_reflec_cub.gif">
	<img src="/assets/images/pthotoRen_3_env_combi_4spheres.gif">
	<figcaption>1) A cup with environment map. &emsp;&emsp;&emsp;&emsp; 2) A complex object with red, blue, green lights.</figcaption>
</figure>

### Hybrid Rendering
<figure>
  <img src="/assets/images/spatialAlg_banny_def_all.png">
  <figcaption>It defers most of the heavy rendering (like lighting) to a later stage. The final image comes from G-buffer which is a combined data of position, nomals, albedo, and specular. In this project, final objects are rendered in deferred shading, whereas debug information such as position of lights, normal vectors, and bounding volumes are rendered in forward shading.</figcaption>
</figure>

## Used External Libraries
 - GLEW: The OpenGL Extension Wrangler Library
 - GLFW: An OpenGL library
 - GLM: C++ mathematics library for OpenGL Shading Language
 - PFD: A free C++11 file dialog library
 - STB: Public domain libraries for C/C++
 - ImGui: Bloat-free Immediate Mode Graphical User interface for C++

## References
 - learnopengl.com
 - OpenGL Programming Guide: The Official Guide to Learning OpenGL
 - OpenGL SuperBible: Comprehensive Tutorial and Reference