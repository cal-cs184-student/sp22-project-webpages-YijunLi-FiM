# Report on CS 184 Project 4: Cloth Sim

## Overview

This project aims to simulate fabric's interaction with physical environment, with tweakable parameters of material, exterior influences and representation. It is also a playground for shader experiments.  

## Part 1: Masses and springs

- Take some screenshots of `scene/pinned2.json` from a viewing angle where you can clearly see the cloth wireframe to show the structure of your point masses and springs.
  | Spring Type |           Screenshot           |
  |-------------|--------------------------------|
  |     All     |     ![](images/p1/all.png)     |
  |   bending   |   ![](images/p1/bending.png)   |
  |   shearing  |   ![](images/p1/shearing.png)  |
  |  structural |  ![](images/p1/structural.png) |</br>

  Every lattice point corresponds to a point mass, and edges represent the springs connecting them. Both bending and structural springs connect to form a regular grid, while shearing springs connect diagonally.  
- Show us what the wireframe looks like (1) without any shearing constraints, (2) with only shearing constraints, and (3) with all constraints.
  |               |           Wireframe            |
  |---------------|--------------------------------|
  |  No shearing  |  ![](images/p1/noshearing.png) |
  | Only shearing |   ![](images/p1/shearing.png)  |
  |      All      |     ![](images/p1/all.png)     |

## Part 2: Simulation via numerical integration

- Experiment with some the parameters in the simulation. Describe the effects of changing the spring constant `ks`; how does the cloth behave from start to rest with a very low `ks`? A high `ks`? What about for `density`? What about for `damping`? For each of the above, observe any noticeable differences in the cloth compared to the default parameters and show us some screenshots of those interesting differences and describe when they occur.
  A higher `ks` makes the fabric feels more taut and bouncy, and a lower `ks` makes it looser and less elastic. Higher `density` exerts more force on every point mass and makes the fabric feel heavier. `damping` acts as an environmental resistance that damps the motion, which in some way can reflect the viscosity of air. </br>
  Several observations:
  |                               |     Notes     |
  |-------------------------------|---------------|
  |    ![](images/p2/taut.png)    | (This doesn't really show on screenshot) With density set to very high (beyond `10000g/cm^2`), part of fabric being stretched taut will not stop bouncing around randomly after the fabric comes to resting position. |
  |  ![](images/p2/toobouncy.png) | When adjusting `density` to a lower value (which is more like real life fabric density) while keeping `ks` at a mismatched value (too high), fabric will come to rest with many ripples, or even start flapping violently. |
  |    ![](images/p2/wat.png)     | This is an extreme case of `density`/`ks` mismatching. More extreme values can cause out of range motions and crash the simulation. |
- Show us a screenshot of your shaded cloth from `scene/pinned4.json` in its final resting state! If you choose to use different parameters than the default ones, please list them.
  With default parameters: ![](images/p2/pinned4.png)

  <video controls>
  <source src="https://user-images.githubusercontent.com/27878967/163852372-ebdeffe9-9adc-45f8-a26d-793ad0e585c0.mp4" type="video/mp4">
  </video> 

  <video controls>
  <source src="https://user-images.githubusercontent.com/27878967/163853701-5ad1b5a4-60f1-45da-b74a-81de7bfa5100.mp4" type="video/mp4">
  </video> 

## Part 3: Handling collisions with other objects

- Show us screenshots of your shaded cloth from `scene/sphere.json` in its final resting state on the sphere using the default `ks = 5000` as well as with `ks = 500` and `ks = 50000`. Describe the differences in the results.
  |   `ks`  |           Result           |
  |---------|----------------------------|
  |    500  | ![](images/p3/ks500.png)   |
  |   5000  | ![](images/p3/ks5000.png)  |
  |  50000  | ![](images/p3/ks50000.png) |</br>

  Note that when `ks` is higher, the fabric becomes more rigid.  
- Show us a screenshot of your shaded cloth lying peacefully at rest on the plane. If you haven't by now, feel free to express your colorful creativity with the cloth! (You will need to complete Part 5 first to show custom colors for anything but the "Wireframe" material.)
  ![](images/p3/plane.png)

  <video controls>
  <source src="https://user-images.githubusercontent.com/27878967/163853833-7277db19-1b7b-4b34-884a-76cde1647ab8.mp4" type="video/mp4">
  </video> 

## Part 4: Handling self-collisions

- Show us at least 3 screenshots that document how your cloth falls and folds on itself, starting with an early, initial self-collision and ending with the cloth at a more restful state (even if it is still slightly bouncy on the ground).
  |                               Stage                               |          Screenshot          |
  |-------------------------------------------------------------------|------------------------------|
  | Cascading onto the plane, no self-collision yet                   | ![](images/p4/start.png)     |
  | Bottom of the fabric starts self-colliding, repelling each other  | ![](images/p4/collision.png) |
  | The fabric comes to a rest after some time                        | ![](images/p4/rest.png)      |
- Vary the `density` as well as `ks` and describe with words and screenshots how they affect the behavior of the cloth as it falls on itself.
  |                          |                                             Note                                             |
  |--------------------------|----------------------------------------------------------------------------------------------|
  | ![](images/p4/ref.png)   | Default parameters                                                                           |
  | ![](images/p4/lowks.png) | As expected, a lower `ks` makes the fabric easier to deform                                  |
  | ![](images/p4/highd.png) | A higher `density` just makes it heavier, which mostly makes it fall faster and more compact |
  </br>
  | $k_s$\\Density | 5 | 50 | 500 |
  | 500   |![](images/p4/5_500.png)|![](images/p4/50_500.png)|![](images/p4/500_500.png)|
  | 5000  |![](images/p4/5_5k.png)|![](images/p4/50_5k.png)|![](images/p4/500_5k.png)|
  | 50000 |![](images/p4/5_50k.png)|![](images/p4/50_50k.png)|![](images/p4/500_50k.png)|
  </br>
  <video controls>
  <source src="https://user-images.githubusercontent.com/27878967/163854028-fb384b76-93b8-48da-b35f-b94294b01a7e.mp4" type="video/mp4">
  </video> 

## Part 5: Shaders

### Diffuse

![](images/p5/diffuse.png)

### Phong

![](images/p5/phong.png)

### Texture

![](images/p5/texture.png)

### Displacement

![](images/p5/displacement_10_1.png)  
![](images/p5/displacement_100_0.02.png)  
![](images/p5/displacement_100_0.05.png)  
![](images/p5/displacement_100_0.25.png)  

### Bump

![](images/p5/bump_2_1.png)  
![](images/p5/bump_2_10.png)  
![](images/p5/bump_10_2.png)  

### Reflection

![](images/p5/mirror1.png)  
![](images/p5/mirror2.png)  

### EC: Displacement Reflection

![](images/p5/dr1.png)  
![](images/p5/dr2.png)  
![](images/p5/dr3.png)  
![](images/p5/dr4.png)  

<video controls>
  <source src="https://raw.githubusercontent.com/cal-cs184-student/sp22-project-webpages-YijunLi-FiM/master/proj4/images/p5/p5.mp4" type="video/mp4">
</video> 
