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
<img src="./images/part-1-grid.png" style="width:100%">|