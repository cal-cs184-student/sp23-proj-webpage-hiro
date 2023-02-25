---
title: Project 2 - MeshEdit
layout: default
---

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# Setup

The project is compiled with `Apple clang version 14.0.0`.
All tests are performed on a 2021 MacBook Pro with Apple M1 Pro CPU (ARM).

# Overview

In the first section of this assignment, we implemented De Casteljau subdivition for Bezier curves and surfaces. 

In the second section of this assignment, we worked with Halfedge data structure. We first implemented an algorithm for computing vertex norm using neighbouring surfaces. We can then do Phong shading using this normal vector. We then implemented local operations including edge flipping and edge splittng and implemented loop-subdivision algorithm using these operations.

# Section I: Bezier Curves and Surfaces

In this section, we explore De Casteljau subdivition for Bezier curves and surfaces.

## Part 1: Bezier Curves with 1D de Casteljau Subdivisions
De Casteljau's algorithm is a recursive algorithm used to evaluate Bezier curves or surfaces. It works by performing **linear interpolation** on two consecutive control points (on the same level) given parameter $$t$$ to generate a set of new intermediate points. The algorightm stops when there is only one single point left, which is the point on the Bezier curve corresponding to the given parameter value $$t$$.

Specifically, given a parameter $$t$$, and a set of control points $$p_i$$ at a specific level, the **linear interpolation** function, or `lerp` for generating control points for the next level is as following

$$p^{'}_{i} = \text{lerp}(p_{i}, p_{i+1}, t)=(1-t)p_{i} + tp_{i+1}$$

where $$p_{i}, p_{i+1}$$ are the consecutive control points on the previous level, and $$p^{'}_{i}$$ is the point generated for the next level.

Below are an example of this algorithm running on a set of 6 control points. 

| Level 0 | Level 1 | Level 2 |
|--------|--------|--------|
|<img src="./images/part-1-s0.png" style="width:100%">|<img src="./images/part-1-s1.png" style="width:100%">|<img src="./images/part-1-s2.png" style="width:100%">|

| Level 3 | Level 4 | Level 5 |
---------|--------|--------|
|<img src="./images/part-1-s3.png" style="width:100%">|<img src="./images/part-1-s4.png" style="width:100%">|<img src="./images/part-1-s5.png" style="width:100%">|


The same curve with one slightly modified control point

<img src="./images/part-1-diff.png" style="width:60%">

An animation of the final evaluation point moving when changing the parameter value $$t$$.  
(**NOTE**: this is an animation, you need to view it on the webpage instead of on the PDF file)

<img src="./images/part-1-mov2.gif" style="width:60%">


## Part 2: Bezier Surfaces with Separable 1D de Casteljau
The separate 1D de Casteljau algorithm can be extended to evaluate Bezier surfaces, which are two-dimensional shapes defined by a network of control points. To evaluate a Bezier surface using the separate 1D de Casteljau algorithm, we first apply the algorithm in one direction (the `u` direction) to obtain 4 intermediate curves. We then apply the algorithm to each of these intermediate curves in the other direction (the `v` direction) to obtain the final value of the surface.

In terms of implementation, we have the follow steps:
1. For each row in `controlLists`, we apply 1D `lerp` with parameter `u` on consecutive control points.
2. So for each row, we will eventually get a single control point with parameter `u`, save the point for later calculation along another direction.
3. With the newly generated control points, we again perform 1D `lerp` with parameter `v`. And similarly, we will eventually get a single point for the Bezier patch with given `u, v`.


<img src="./images/part-2.png" style="width:60%">

# Section II: Triangle Meshes and Half-Edge Data Structure
## Part 3: Area-Weighted Vertex Normals

## Part 4: Edge Flip

Before Flip:

<img src="./images/before-sketch.png" style="width:60%">

After Flip:

<img src="./images/part-4-after-sketch.png" style="width:60%">

## Part 5: Edge Split

Before Split:

<img src="./images/before-sketch.png" style="width:60%">

After Split:

<img src="./images/part-5-after-sketch.png" style="width:60%">


## Part 6: Loop Subdivision for Mesh Upsampling


