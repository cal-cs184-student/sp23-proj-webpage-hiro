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

Here are two screenshots showing the difference and effect of lower and higher spring constant. Notice there are more wrinke in the lower `ks` image compared to the one with higher `ks`.

| `ks` = 5000 (Default) | `ks` = 50000 |
|------------|--------------|
|<img src="./images/part-2-ks-5000.png" style="width:100%">|<img src="./images/part-2-ks-50000.png" style="width:100%">|

## Effect of density

Density determines the mass of individual point mass, thus adding up determines the mass of the entire cloth. We expect a higher density would let us simulate a heavier cloth, while a lower density would let us simulate a lighter cloth.

Therefore, for the scene `pinned2.json`, we would observe the cloth to have a larger deformation (i.e., more wrinkles) for higher density value. This is because a heavier point mass would exert a larger gravity force on each spring (since $$f = mg$$) and a ligher point mass would exert a smaller gravity force. Therefore, with higher density, there will be more deformation to the spring, thus more deformation to the simulated cloth.

Below are two screenshots showing the difference of lower and higher density. 

| Density = 1 | Density = 15 (Default) |
|------------|--------------|
|<img src="./images/part-2-den-1.png" style="width:100%">|<img src="./images/part-2-den-15.png" style="width:100%">|

## Effect of damping

Damping simulates the loss in energy during the simulation step. With higher damping, we simulate the case where much energy (velocity) accumulated from previous step has been lost during the simulation step, thus there are less "inertia" hence less movement in cloth. With lower damping, we simulate the case where the energy (velocity) from previous step has been well preserved, hence the cloth would be affected more by previous movement / veolocity during current simulation step.

This effect is most clearly observed as with lower damping, the cloth takes much longer to settle to the rest state. This is because the cloth preserves a lot of velocity / energy from previous state, thus the cloth always overshoot the equilibrium state, and will be pulled back by various external force (and overshoot again etc.). Thus it takes longer (much much longer) for the cloth to settle down. However, with higher damping, since the cloth will be mostly affected by only the current force exerting on object, the cloth settle much quicker (since there is less overshoot).

Below are some screenshots taken with different damping. To better illustrate damping, we also included a GIF for each screenshot.

| Damping = 0 | Damping = 0.2% (Default) |
|------------|--------------|
|<img src="./images/part-2-dan-0.png" style="width:100%">|<img src="./images/part-2-dan-2.png" style="width:100%">|
|<img src="./images/part-2-dan-0.gif" style="width:100%">|<img src="./images/part-2-dan-2.gif" style="width:100%">|

# Extra Credit: Wind Simulation

We can view wind as an invisible viscous fluid (i.e., air) trying to push the cloth in a certain direction. Provot (1995) proposed that we can model such behaviour by the following equation

$$f_{vi} = c_{vi} [n_{i,j} \cdot (u_{fluid} - v_{i,j})] n_{i,j} \hspace{1em} [1]$$

Where $$f_{vi}$$ is the wind force that is exerted on the object, $$c_{vi}$$ is a constant representing the intensity of the force, $$u_{fluid}$$ is the direction of the wind (we assume wind blowing uniformly in one direction), and finally $$n_{i,j}$$ and $$v_{i,j}$$ are the normal vector and velocity of the point mass at position $$(i, j)$$.

Notice that compared to gravity force, wind force exerts a different amount of force on each point mass depending on some properties (velocity and normal vector) of the point mass. Therefore, we cannot simply add another `Vector3D` to the list of external forces. After much consideration, we decided to introduce another class for generalized external forces. The `ExternalForce` class is created as an interface class that provides one method interface `compute_force` with signiture   
```{c++}
Vector3D compute_force(PointMass pm, double mass, ouble delta_t);
```
This method would compute the exerted force at the given point mass.

Then both Gravity force and Wind force would inherite this base class and implement the `compute_force` method. For gravity, it is simply returniing a constant `gravity_force * mass` for all point  mass; and for Wind, it computes the force exerted on the point mass as described above.

Here are some screenshot showing wind effect on cloth:

For scene `pinned2.json` with wind direction $$(\frac{1}{\sqrt{2}}, 0, \frac{1}{\sqrt{2}})$$ with different intensity

| $$c_{vi} \approx 0.3$$ | $$c_{vi} \approx 0.6$$ | $$c_{vi} \approx 1$$ |
|----------|------------|-------------|
|<img src="./images/part-6-pin2-1.png" style="width:100%"> | <img src="./images/part-6-pin2-2.png" style="width:100%">| <img src="./images/part-6-pin2-3.png" style="width:100%">|




# Reference
[1] Provot, X. (1995, May). Deformation constraints in a mass-spring model to describe rigid cloth behaviour. In Graphics interface (pp. 147-147). Canadian Information Processing Society.
