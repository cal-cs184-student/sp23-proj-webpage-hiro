---
title: Project 3 - Pathtracer
layout: default
---

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# Setup

The project is compiled with `Apple clang version 14.0.0`.
All tests are performed on a 2021 MacBook Pro with Apple M1 Pro CPU (ARM).

# Overview

## Part 1: Ray Generation and Scene Intersection

Our code in this part implements the ray-triangle intersection test for a given input `Ray r` and a `Triangle` primitive.

1. First, we use the Moller-Trumbore algorithm is used to compute the intersection point of the ray with the triangle.
2. After the Moller-Trumbore algorithm, we get `[t, b_1, b_2]`. Then we use the variables to test if the intersection point is inside the triangle and within the line segment of the ray.
    - It is inside the line segment of the ray if `t <= t_max` and `t >= t_min`.
    - It is inside the triangle if satisfies the barycentric constraint `b1 >= 0 && b1 <= 1 && b2 >= 0 && b2 <= 1 && 1 - b1 - b2 >= 0 && 1 - b1 - b2 <= 1`
3. If the above conditions are met, then the intersection data `isect` is updated accordingly. The method returns true if an intersection occurs and false otherwise.

| CBempty                                                | dragon                                                |
| ------------------------------------------------------ | ----------------------------------------------------- |
| <img src="./images/p1_CBempty.png" style="width:100%"> | <img src="./images/p1_dragon.png" style="width:100%"> |

## Part 2: Bounding Volume Hierarchy

## Part 3: Direct Illumination

## Part 4: Global Illumination

## Part 5: Adaptive Sampling
