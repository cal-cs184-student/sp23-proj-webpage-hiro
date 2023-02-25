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
The 1D de Casteljau algorithm can be extended to evaluate Bezier surfaces, which are two-dimensional shapes defined by a network of control points. To evaluate a Bezier surface using the 1D de Casteljau algorithm, we first apply the algorithm in one direction (with parameter value $$u$$) to obtain a set of evaluated points on this direction. We then use this set of points as the new input control points for the other direction (with parameter value $$v$$). After evaluation, we will eventually get only 1 point as the final evaluated point for paremeter value $$(u, v)$$.

In terms of implementation, we have the follow steps:
1. For each row in `controlLists`, we apply 1D `lerp` with parameter `u` on consecutive control points.
2. So for each row, we will eventually get a single control point with parameter `u`, save the point for later calculation along another direction.
3. With the newly generated control points, we again perform 1D `lerp` with parameter `v`. And similarly, we will eventually get a single point for the Bezier patch with given parameter value `(u, v)`.

Here is a screenshot of `bez/teapot.bez`.

<img src="./images/part-2.png" style="width:60%">

# Section II: Triangle Meshes and Half-Edge Data Structure
## Part 3: Area-Weighted Vertex Normals

## Part 4: Edge Flip

We believe the tip from the assignment prompt is very useful. We hand-drew an example of an edge flip (shown below) with all elements (before and after the flip) labeled. We then used this hand-drew example to re-assign any element for an edge flip.

|Before Flip: | After Flip: |
|-------------|-------------|
|<img src="./images/before-sketch.png" style="width:100%">|<img src="./images/part-4-after-sketch.png" style="width:100%">|

Our implementation is as following:
1. Use `e0` to get `h0`
2. From `h0` and `h0->twin()`, we were able to traverse the two triangles and get all the elements shown in the "Before Flip" figure
3. Re-assign all the half-edges to their new neighbours shown in the "After Flip" figure
4. Re-assign all vertices, edges and faces to theri new half-edge

We later noticed the half-edge associated with `e1`, `e2`, `e3` and `e4` does not change, so we removed these four edges from step 4.

We were able to implement edge split without any bug in the first try, so our exeperience for this part is very smooth.

Below are some screenshots of `dae/teapot.dae` with some edge flips

|Original: | After Flip: |
|----------|-------------|
|<img src="./images/part-4-0.png" style="width:100%">|<img src="./images/part-4-1.png" style="width:100%">|

## Part 5: Edge Split

Similar to Part 4, we hand-drew an before vs. after example for splitting triangle mesh.

|Before Split: | After Split: |
|--------------|--------------|
|<img src="./images/before-sketch.png" style="width:100%">|<img src="./images/part-5-after-sketch.png" style="width:100%">|

Again, our implementation is as following:
1. Use `e0` to get `h0`. Then, use `h0` and `h0->twin()` to get all elements that appeared on the "Before Split:" figure
2. We create all new element using the `newHalfedge`, `newEdge`, `newFace` and `newVertex` function.  
   In particular, we need to create:  
   - new vertex `M`;  
   - new half-edges `h3`, `h4`, `h5`, `ht3`, `ht4` and `ht5`; 
   - new edges `e5`, `e6` and `e7`; and finally,
   - new faces `f2` and `f3` 

   For the new position of vertex `M`, we simply used `0.5 * (v0.position + v1.position)` as a default. 
3. Assign new neighbours to all hald-edges according to the "After Split:" figure.
4. Assign new half-edges to all elements according to the "After Split:" figure.

Below are some screenshots of `dae/teapot.dae` with some edge splits and flips

|Original Model|After Splits| After Flips|
|--------------|--------------|----------|
|<img src="./images/part-5-0.png" style="width:100%">|<img src="./images/part-5-1.png" style="width:100%">|<img src="./images/part-5-2.png" style="width:100%">|

### Extra Credit

We also implemented edge split for boundary edge.

The implementation is very similar to above, except we ignore the lower half triangle shown in our hand-drew figures. 

Two details that we need to handle for the boundary case is 
1. In step 1 of the original implementation, we need to check if `e0->halfedge()` is the halfedge on a boundary or on a regular face. If `e0->halfedge()` is on a boundary, we need to use `e0->halfedge()->twin()` (i.e., the half-edge that is not on boundary) as `h0`.
2. In step 3, instead of assigning `ht3` to a new face, we assign `ht3` to the same boundary face `ht0` is on.

Below are some screenshots of `dae/beetles.dae` before and after splitting some boundary edges.

| Original Model | Split Boundary |
|----------------|----------------|
|<img src="./images/part-5-extra-0.png" style="width:100%">|<img src="./images/part-5-extra-1.png" style="width:100%">|

## Part 6: Loop Subdivision for Mesh Upsampling


