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
De Casteljau's algorithm is a recursive algorithm used to evaluate Bezier curves. It works by performing **linear interpolation** function on two consecutive control points (two control points should be in the same level), until there is only one single point at certain level, which is the point on the Bezier curve corresponding to the given parameter value.

With a parameter `t`, and a set of control points at a specific level, the **linear interpolation** function, or `lerp` is the follow
$$p^{'}_{i} = lerp(p_{i}, p_{i+1}, t)=(1-t)p_{i} + tp_{i+1}$$
where \(p_{i}, p_{i+1}\) are the consecutive points at the same level, and \(p^{'}_{i}\) is the point generated for the next level. And the implementation is as the following.
```cpp
    T inline lerp(const T& a, const T& b, double t) {
        return (1 - t) * a + t * b;
    }
```
We evaluated the curve by recursively performing `lerp` at one level, and generating the control points for the next level, until there is one single point at certain level.
```cpp
    std::vector<T> npts(points.size() - 1, T()); // init new control points for the next level
    for (int i = 0; i < points.size() - 1; ++i)  // perform lerp on consecutive points
    { 
        npts[i] = lerp(points[i], points[i + 1], t);
    }
    return npts; // return the points for the next level
```

| Step 0 | Step 1 | Step 2 |
|--------|--------|--------|
|<img src="./images/part-1-s0.png" style="width:100%">|<img src="./images/part-1-s1.png" style="width:100%">|<img src="./images/part-1-s2.png" style="width:100%">|

| Step 3 | Step 4 | Step 5 |
---------|--------|--------|
|<img src="./images/part-1-s3.png" style="width:100%">|<img src="./images/part-1-s4.png" style="width:100%">|<img src="./images/part-1-s5.png" style="width:100%">|


The curve with one slightly modified control point

<img src="./images/part-1-diff.png" style="width:60%">

A animation of the final evaluation point moving along the `t`.
(NOTE: this is an animation, you need to view it on the webpage instead of on the PDF file)

<img src="./images/part-1-mov2.gif" style="width:60%">


## Part 2: Bezier Surfaces with Separable 1D de Casteljau
<img src="./images/part-2.png" style="width:60%">

# Section II: Triangle Meshes and Half-Edge Data Structure
## Part 3: Area-Weighted Vertex Normals

## Part 4: Edge Flip
## Part 5: Edge Split
## Part 6: Loop Subdivision for Mesh Upsampling


