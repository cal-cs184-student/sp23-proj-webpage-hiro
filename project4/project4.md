---
title: Project 4 - Cloth Sim
layout: default
---

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# Setup

This page is avaliable at <https://cal-cs184-student.github.io/sp23-proj-webpage-hiro/project4/project4.html>

The project is compiled with `Apple clang version 14.0.3`

# Part 1: Masses and Springs

In this part, we built a evenly spaced grid of point masses connected by springs to represent a cloth. Each spring represents one type of force, `STRUCTUAL`, `SHEARING` and `BENDING`.

Below are some screenshot of the wireframe of the cloth. 

<img src="./images/part-1-grid.png" style="width:100%">
<img src="./images/part-1-grid-zoomed.png" style="width:100%">

Here is the grid with certain constraint turned on and off

**No `SHEARING`** 
<img src="./images/part-1-no-shearing.png" style="width:100%">

**Only `SHEARING`** 
<img src="./images/part-1-shearing.png" style="width:100%">

**Everything**
<img src="./images/part-1-grid.png" style="width:100%">

# Part 2: Simulation via Numerical Integration

In this part, we added simulation of different types of external and spring forces that is exerted on each point mass. We implmeneted the force simulation using Verlet integral over `delta_t`. We also added movement constraint to make the simulation less "dramatic".

## Effect of spring constant `ks`

Spring constant `ks` is basically the stiffness of the spring. Higher `ks` means the spring is harder to stretch and lower `ks` means the spring is easier to stretch. Intuitively speaking, cloth with higher `ks` is harder and more rigid and cloth with lower `ks` is softer.

Therefore, we will observe a larger deformation in cloth with a lower `ks` value. In particular, we will see more wrinkles in cloth with lower `ks` compared to cloth with higher `ks`, since the cloth with lower `ks` can be easily deformed.

Here is a screenshot showing the difference and effect of lower and higher spring constant. Notice there are more wrinke n the lower `ks` image compared to the one with higher `ks`.

| `ks` = 5000 (Default) | `ks` = 50000 |
|------------|--------------|
|<img src="./images/part-2-ks-5000.png" style="width:100%">|<img src="./images/part-2-ks-50000.png" style="width:100%">|
