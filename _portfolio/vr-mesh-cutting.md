---
title: "VR Mesh Cutting"
permalink: /portfolio/vr-mesh-cutting/
date: 2020-10-15
excerpt: "This is a tech demo in VR environment where objects can be cut as the user does."
header:
  image: /assets/images/vrMeshCut_main_wide.png
  teaser: /assets/images/vrMeshCut_play_1.gif
company: "Academic Project"
engine: "Unity"
platform: "Windows PC, HTC Vive"
skills: "C#, Graphics, Linear Algebra"
role: "Gameplay Engineer"
responsibilities: "Mesh Cutting, Sawing Behavior"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## In-Game Videos
<figure class="half">
	<img src="/assets/images/vrMeshCut_play_1.gif">
	<img src="/assets/images/vrMeshCut_play_2.gif">
    <figcaption>Mesh cutting without a blade width</figcaption>
</figure>

## Experiment

### Features
 - Mesh Cutting with a Blade Width 
 - Concave Decomposition
 - Optimization for Real-Time Performance

### Convex Cutting
<figure>
  <img src="/assets/images/vrMeshCut_convex_plan_1.png">
</figure>
<figure>
  <img src="/assets/images/vrMeshCut_convex_plan_2.png">
</figure>

```csharp
// The final data to rearrange vertex indices to split triangles
private struct StartEndPairPoints
{
  CuttingDepthPoint start
  CuttingDepthPoint end
  List<CuttingStartPoints> cuttingPointsList
}

// Each depth point from a blade
private struct CuttingDepthPoint
{
  // the start index in the triangle data of a mesh
  int triangleIndex
  // the intersected point between a ray and the object
  float[3] point
  float dist
  // the start point of the sliced edge in clock-wised direction
  float[3] pointStart
  // the end point of the sliced edge in clock-wised direction
  float[3] pointEnd
  // the relative direction of the blade and the triangle order
  bool isStartLeft
}

// Each sliced edge is refined as CuttingStartPoints
private struct CuttingStartPoints
{
  int triangleStartIndex
  // whether one edge is cut or not
  int isolatedIndex
  // the first vertex of a sliced edge in clock-wised order
  int triangleIndex1
  // the second one
  int triangleIndex2
  float[3] pointStart
  float[3] pointEnd
  bool isStartLeft
  // whether the width of the blade is exactly on a vertex or not
  bool isOnBlade
}
```

<figure>
  <img src="/assets/images/vrMeshCut_convex_done.png">
</figure>

### Concave Decomposition
<figure>
  <img src="/assets/images/vrMeshCut_concave_plan_1.png">
</figure>

The above description is the main concept of the concave decomposition for this project. The blue and red dots are the depth point of each object. After an object is sliced into two convex objects, the two objects which are at the left and right side of the blade will test collision check with other objects only within the box.

<figure>
  <img src="/assets/images/vrMeshCut_concave_plan_2.png">
</figure>

### Optimization
<figure>
  <img src="/assets/images/vrMeshCut_optimization_1.png">
</figure>

**Reducing the calculation range** is one of methods to optimize the performance. It partitions the space using a dot product between the perpendicular plane and each vertex so that only the upper trianges are conducted a complex calculation.

## References
 - [Mesh Manipulation](https://www.raywenderlich.com/3169311-runtime-mesh-manipulation-with-unity)
 - [Convex Mesh Slicing](https://github.com/hugoscurti/mesh-cutter)
 - [Metal Gear Rising – Slicing](https://simonschreibt.de/gat/metal-gear-rising-slicing)
 - [Concave Decomposition](https://gamedev.stackexchange.com/questions/53142/decomposing-a-concave-mesh-into-a-set-of-convex-meshes)
 - [Constructive Solid Geometry](https://github.com/evanw/csg.js)
 - [Addaptive Virtual Node Algorithm](https://www.math.ucla.edu/~jteran/papers/WJST14.pdf)
 - [Chainsaw with Voxel Wood](https://twitter.com/pushmatrix/status/1328710719269990401)
