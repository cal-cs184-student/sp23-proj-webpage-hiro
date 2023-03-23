---
title: Project 3 - Pathtracer
layout: default
---

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# Setup

This page is avaliable at <https://cal-cs184-student.github.io/sp23-proj-webpage-hiro/project3/project3.html>

The project is compiled with `Apple clang version 14.0.0` and `Linux GCC`.

# Overview

## Part 2: Microfacet Material

| $$\alpha=0.005$$                                              | $$\alpha=0.05$$                                              | $$\alpha=0.25$$                                              | $$\alpha=0.5$$                                              |
| ------------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| <img src="./images/part-2-dragon-005.png" style="width:100%"> | <img src="./images/part-2-dragon-05.png" style="width:100%"> | <img src="./images/part-2-dragon-25.png" style="width:100%"> | <img src="./images/part-2-dragon-5.png" style="width:100%"> |

Increasing the value of α will increase the amount of scattering and reflection of light from the surface, resulting in a more diffused and blurry appearance. Decreasing the value of α will result in less scattering and reflection and a sharper appearance. In the case of rendering scene `CBdragon_microfacet_au.dae`, increasing the value of α from 0.005 to 0.5 would increase the roughness of the gold, causing it to appear increasingly diffused. A lower α value would result in a sharper appearance. The effect of the α parameter can be particularly noticeable in the highlights and reflections on the surface of the gold material.

| Hemisphere Sampling                                        | Importance Sampling                                        |
| ---------------------------------------------------------- | ---------------------------------------------------------- |
| <img src="./images/part-2-bunny-H.png" style="width:100%"> | <img src="./images/part-2-bunny-I.png" style="width:100%"> |

Rendering scene `CBbunny_microfacet_cu.dae` using importance sampling instead of cosine hemisphere sampling resulted in a reduction in noise and an increase in the quality of the highlights and reflections on the surface of the copper material.

| Material: Iron                                               |
| ------------------------------------------------------------ |
| <img src="./images/part-2-dragon-fe.png" style="width:100%"> |

The material we picked for rendering the scene is iron. The parameters are $$\eta=2.9304$$ and $$k=2.9996$$.

## Part 4: Depth of Field

Both the pinhole camera model and the thin-lens camera model are simplified models used in computer graphics and computer vision to simulate and analyze the behavior of cameras. The main difference between the two models is that the pinhole camera model uses a small aperture or hole to project the image onto the camera sensor or film, while the thin-lens camera model uses a lens to refract and focus the light onto the sensor or film. The thin-lens camera model is more complex than the pinhole camera model and can simulate more realistic camera behaviors, such as depth of field and lens distortion.
