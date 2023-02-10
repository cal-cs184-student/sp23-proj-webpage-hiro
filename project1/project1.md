---
title: Project 1 - Restarizing Triangle
layout: default
---

# Introduction and Setup

The project is compiled with `Apple clang version 14.0.0`.

All tests are performed on a 2021 MacBook Pro with Apple M1 Pro CPU (ARM) with default resolution 1600x1200 (unless otherwise specified).

# Part 1: Rasterizing Simple Triangle
We implemented the following algorithm to rasgerize simple triangles
1. Find the bounding box for the triangle by finding $x_{max} = max(x_0, x_1, x_2)$, $x_{min} = min(x_0, x_1, x_2)$ and $y_{max} = max(y_0, y_1, y_2)$ and $y_{min} = min(y_0, y_1, y_2)$.
2. Loop over all each pixel $(x, y)$ with $x_{min} <= x <= x_{max}$ and $y_{min} <= y <= y_{max}$ and perform point-in-triangle test (described in lecture) on the point $(x + 0.5, y + 0.5)$ to determine if this point is in the triangle with vertices $(x_0, y_0), (x_1, y_1), (x_2, y_2)$.
3. Set `sample_buffer[y * width + x]` with the corresponding color if the point-in-triangle returns `true` in the above step.

Since the winding-order of the vertices are not fixed for our reasterizer, we need to modify the point-in-triangle test a little bit for our purpose. In particular, after computing `L0`, `L1` and `L2` using the formula from the lecture, we check if `(L0 >= 0 && L1 >= 0 && L2 >= 0) || (L0 <= 0 && L1 <= 0 && L2 <= 0)`. That is, the point is in the triangle if either the point is 'inside' all three lines or 'outside' all three lines. Notice also that all checks allows the case `== 0` since the prompt mentioned that we should render points on the bondary as well.

This implementation is no worse than the one that checkes every point in the bounding box because it does exactly checking every point in the bounding box. We implemented some optimizations like loop-reordering and early breaking which you can find more information on in the Extra Credit section below.

<img src='./images/part_1.png' width='480' height='360'>


## Extra Credit:
From CS267, we learned that loop reordering could help us gain extra performance when operating on matrix (or matrix-like) data stored in row or column majored format. Since `sample_buffer` is basically a 2D matrix stored in row major format, we decided to implement loop reordering. Loop re-ordering is implemented simply as swaping the outer and inner loop variable. 

We basically changed the loop order from
```cpp
for (int x = 0; x < width; ++x) {
    for (int y = 0; y < height; ++y) {
        // work with the pixel at (x, y)
        ...
    }
}
```
To
```cpp
for (int y = 0; y < width; ++y) {
    for (int x = 0; x < height; ++x) {
        // work with the pixel at (x, y)
        ...
    }
}
```

We also noticed that triangle is convex. Therefore, when scanning over a row, once we left the region inside triangle, we would never re-enter the triangle again in the same row. This means that we can break the ineer loop (which scans over a row) early if we detected that we have left the region inside the triangle.

<img src="./images/half_zigzag.jpg" width="300" height="300">

The above images shows an example of the execution. We would start our inner loop at the left border, then break on the orange pixel and skip the rest of the line. 

Here is an comparison of run time for some example images with optimization on and off. All run times are average of three runs.
|File Name|No Optimization|Loop Reordering|Early Break and Loop Reordering|

|---------|---------------|---------------|-----------|
|`basic/test3.svg`|17.93ms|17.46ms|11.40ms |
|`basic/test4.svg`|0.984ms | 0.975ms | 0.561ms|
|`basic/test5.svg`|2.98ms| 2.99ms | 1.86ms|

We have, on average of the three test cases, archieve 38.99% improvement in rendering speed with only these two very simple optimizations implemented. (It takes less than 10 lines of code changes.)


# Part 2: Super Sampling

On a high-level, our approach towards super-sampling is to use `sample_buffer` as a high-resolution screen space. Then, on `resolve_to_framebuffer` call, we will downsample the `sample_buffer` array to fill the `framebuffer`.

Here is our modified rasterizing pipeline:
1. Resize the `sample_buffer` array to size `width * height * sample_rate`
2. On `rasterize_triangle`, scale up `x0`, `x1`, `x2`, `y0`, `y1` and `y2` by `sqrt(sample_rate)` (that is `x0` becomes `x0 * sqrt(sample_rate)` etc.)
3. Perform `rasterize_triangle` as in task 1 with the scaled up coordinates
4. On `resolve_to_framebuffer`, add an inner loop that takes the average of the colors in the `sqrt(sample_rate)` by `sqrt(sample_rate)` sample area. The pesudo code looks like following : 
```cpp
for (int x = 0; x < width; ++x) {
    for (int y = 0; y < height; ++y) {
        // average the sample area
        Color avg = Color::Black;
        for (int i = 0; i < sqrt(sample_rate); ++i) {
            for (int j = 0; j < sqrt(sample_rate); ++j) {
                avg += (1 / sample_rate) * sample_buffer[x * sqrt(sample_rate) + i][y * sqrt(sample_rate) + j];
            }
        }
        rgb_framebuffer_target[x][y] = avg;
    }
}
```
For simplicity, the `sample_buffer` and `rgb_framebuffer_target` here are represented as a 2D array. In practice, coordinates are translated into its appropriate index.

Nyquist Theorem suggests that aliasing is an artifact from signal frequence exceeds Nyquist frequency (half the sampling frequency). Therefore, in order to reduce or eliminate alicasing, we could either increase the sampling frequency (which, as a result, increases Nquist frequency) or reduce the signal frequency. 

However, as we are limited by the hardware we have, it would be difficult or costly to increase sampling frequency, therefore, it is more feasiable to reduce signal frequency. That is where supersampling comes in. 

Supersampling works similar to applying a 1-pixel box-blur to the `sqrt(sample_rate)` by `sqrt(sample_rate)` area. This way, the signal frequency would be reduced (as we blured the original image), thus reducing aliasing.

We can clearly see this "blur" (caused by supersampling) in motion in the three figures below.

|Sample Rate = 1|Sample Rate = 4|Sample Rate = 16|
|---|---|---|
|<img src="./images/part_2_r1.png" style="width:100%">|<img src="./images/part_2_r4.png" style="width:100%">|<img src="./images/part_2_r16.png" style="width:100%">

As we can see from the figures, with higher supersampling rate, the less aliasing appears. This is because with higher supersampling rate, we are taking average color of a larger area. This is effectively taking a box-blur on a larger box. That is, we are eliminating (filtring out) more of high frequency signals in the the original triangles. Thus, we get less aliasing as an result.

# Part 3: Transformation
We have created a running robot. I applied rotation to part of the arms and the legs. Then adjusted positions (using `translate`) of these components. Lastly, I applied a slight rotation to the entire body (torso + arms + legs) to create a feeling of speed.

<img src="./images/robot_running.png" style="width:50%">

# Part 4: Barycentric Coordinate