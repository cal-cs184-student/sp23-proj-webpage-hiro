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

### Extra Credit:

We also implemented edge split for boundary edges.

The implementation is very similar to above, except we ignore the lower half triangle shown in our hand-drew figures. 

Two details that we need to handle for the boundary case is 
1. In step 1 of the original implementation, we need to check if `e0->halfedge()` is the halfedge on a boundary or on a regular face. If `e0->halfedge()` is on a boundary, we need to use `e0->halfedge()->twin()` (i.e., the half-edge that is not on boundary) as `h0`.
2. In step 3, instead of assigning `ht3` to a new face, we assign `ht3` to the same boundary face `ht0` is on.

Below are some screenshots of `dae/beetle.dae` before and after splitting some boundary edges.

| Original Model | Split Boundary |
|----------------|----------------|
|<img src="./images/part-5-extra-0.png" style="width:100%">|<img src="./images/part-5-extra-1.png" style="width:100%">|

## Part 6: Loop Subdivision for Mesh Upsampling

With edge flip and edge split implemented correctly, loop subdivision is very simple to implement. We implemented loop-subdivition exactly as described in lecture and in the assignment prompt.

Here is an overview of our implementation:
1. We implemented two helper methods to compute the new position of a vertex from an existing vertex and compute the position of a new vertex given an edge. These two methods behaves exactly as the description in the assignment prompt.
2. For each vertex in the mesh, compute its new position and stored in `v->newPosition` and set `v->isNew` to `false`
3. For each edge in the mesh, compute the new vertex associated with the edge and store this position in `e->newPosition` and set `e->isNew` to `false`
4. For each exsiting edges `e`, split these edges by calling `mesh.splitEdge(e)`

   To get the set of existing edges, we noticed that any new edge created are inserted to the end of the list. Therefore, to iterate over all existing edges, we basically need to iterate over the first `N` edges where `N` is the total number of existing edges. Any edge created during the process would be in position `i > N`.  
   
   In addition, we also modified the `splitEdge` method to set `e5->isNew` and `e7->isNew` to `true` (see the hand-drew figure in Part 5 to see which edges are `e5` and `e7`) as well as set the new vertex `M->isNew` to `true`.

   Lastly, we set `M->newPosition = e->newPosition`
5. For each edge `e` currently in the mesh, if `e->isNew` and this edge connect a new vertex to an old vertex, flip the edge by calling `mesh.flipEdge(e)`.
6. For each vertex `v` in the current mesh, set the position to its new position (i.e., `v->position = v->newPosition`)

We noticed that sharp edges and corners gets rounded out when doing loop sub-division. We believe this is due to at each level of subdivision, when we split edge on the sharp edge, we compute the new position of the vertex weighted on all neighbouring vertecies. The neighbouring vertecies basically "pulls" the sharp edge back everytime we try to upsample. Therefore, the sharp edge becomes more rounded at the end. 

This could be countered by pre-splitting near the sharp edge to make all negibhouring vertecies closer to the sharp edge. That way, there will be less "pull back" which could then preserve the sharpness of the edge.

Below are screen shots of the `dae/cube.dae` upsampling with and without pre-splitting. Notice that the edge on the upper-right of the screen was able to preserve (a little bit) with some pre-splitting

**Without Pre-splitting**

| Level 0 | Level 1 |
|---------|---------|
|<img src="./images/part-6-0-0-0.png" style="width:100%">|<img src="./images/part-6-0-0-1.png" style="width:100%">|

| Level 2 | Level 3|
|---------|--------|
|<img src="./images/part-6-0-0-2.png" style="width:100%">|<img src="./images/part-6-0-0-3.png" style="width:100%">|

**With Pre-splitting**

| Level 0 | Level 1 |
|---------|---------|
|<img src="./images/part-6-0-1-0.png" style="width:100%">|<img src="./images/part-6-0-1-1.png" style="width:100%">|

| Level 2 | Level 3|
|---------|--------|
|<img src="./images/part-6-0-1-2.png" style="width:100%">|<img src="./images/part-6-0-1-3.png" style="width:100%">|

We also noticed that `dae/cube.dae` becomes slightly asymmetric after some rounds of subdivition. This is because each face (geometric fase, not mesh face) of the cube is divided to only two triangles, which means the four vertecies on each face of the cube have different degrees (i.e., different number of neighbours). This further means that when computing the new position of the vertex, vertecies with different degrees would not move symmetrically, which ultimately caused the asymmetry at the end.

To alleviate this effect, we can pre-split each face of the cube to be consisted with four triangular mesh. This way, all the vertecies will share the same number of neighbours and would move symmetrically.

Here are some screenshots for this effect and our pre-split

**Without Pre-splitting**

| Level 0 | Level 1 |
|---------|---------|
|<img src="./images/part-6-1-0-0.png" style="width:100%">|<img src="./images/part-6-1-0-1.png" style="width:100%">|

| Level 2 | Level 3|
|---------|--------|
|<img src="./images/part-6-1-0-2.png" style="width:100%">|<img src="./images/part-6-1-0-3.png" style="width:100%">|

**With Pre-splitting**

| Level 0 | Level 1 |
|---------|---------|
|<img src="./images/part-6-1-1-0.png" style="width:100%">|<img src="./images/part-6-1-1-1.png" style="width:100%">|

| Level 2 | Level 3|
|---------|--------|
|<img src="./images/part-6-1-1-2.png" style="width:100%">|<img src="./images/part-6-1-1-3.png" style="width:100%">|

### Extra Credit:

We also implemented loop-subdivision on boundary faces and edges. Basically, it is exactly the same implementation compared to the regular algorithm, except on how we compute new vertex positions for new and old vertecies on the boundary edge. 

After some investigation, we found out that for the new position associated to an existing vertex $$V$$, the new position will be $$\frac{1}{8}(A + B) + \frac{3}{4}V$$ where where $$A$$ and $$B$$ are the two neighbouring vertecies along the boundary edge.

The new potision associated to a new vertex after splitting edge $$E$$ is simply $$\frac{1}{2} (A + B)$$ where $$A$$ and $$B$$ are the two vertecies on the two ends of the edge $$E$$.

Below is an example of subdividing a mesh with boundaries (`dae/beetle.dae`). 

| Level 0 | Level 1 |
|----------------|----------------|
|<img src="./images/part-6-extra-0.png" style="width:100%">|<img src="./images/part-6-extra-1.png" style="width:100%">|