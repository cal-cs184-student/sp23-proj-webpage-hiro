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

In this part, we constructs a BVH (Bounding Volume Hierarchy) tree data structure from a vector of primitives. The BVH tree is built using a top-down recursive approach, starting with the root node that encloses all primitives, and recursively splitting the primitives into smaller groups until the maximum leaf size is reached.

The `construct_bvh` function takes a vector of primitives and a maximum leaf size as input arguments, and calls the `split_bvh` function with the same input arguments. In general, the `split_bvh` function recursively splits the primitives into smaller groups, and returns a pointer to the current BVH node.

1. First, it calculates the bounding box of all the primitives in the given range, and calculates the average centroid of all the primitives. It then creates a new BVH node with the bounding box, and checks if the number of primitives in the range is less than or equal to the maximum leaf size. If so, it sets the start and end iterators of the current node to the input iterators and returns the node as a leaf node.
2. If the number of primitives is greater than the maximum leaf size, it needs to split the primitives into two smaller groups. To do this, it first calculates the number of primitives on the left and right sides of the average centroid along each axis. It then chooses the axis with the smallest difference between the number of primitives on the left and right sides, and calculates the number of primitives on each side of the split along that axis.
3. Next, it performs an in-place partition of the primitives based on their centroid coordinates along the chosen axis, such that all the primitives on the left side of the split come before all the primitives on the right side. It then recursively calls the split_bvh function on the left and right groups of primitives, and sets the left and right child nodes of the current node to the returned nodes
4. Returns the current node pointer.

| CBbunny                                                | CBlucy                                               |
| ------------------------------------------------------ | ---------------------------------------------------- |
| <img src="./images/p2_CBbunny.png" style="width:100%"> | <img src="./images/p2_cblue.png" style="width:100%"> |

## Part 3: Direct Illumination

## Part 4: Global Illumination

## Part 5: Adaptive Sampling
