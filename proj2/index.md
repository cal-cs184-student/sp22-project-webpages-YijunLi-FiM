# Report on CS 184 Project 2: Mesh Edit

[Link to Report - Yimo Xu](https://cal-cs184-student.github.io/sp22-project-webpages-axmmisaka/proj2/index.html)
[Link to Report - Yijun Li](https://cal-cs184-student.github.io/sp22-project-webpages-YijunLi-FiM/render.html?src=proj2/index.md)

## Overview

This project explores typical methods of geometric modeling, namely Bezier Curves/Surfaces and Half Edge data structure that's commonly used to construct mesh. 

## Task 1: Bezier Curves with 1D de Casteljau's Subdivision
- Briefly explain de Casteljau's algorithm and how you implemented it in order to evaluate Bezier curves. <br/>
  A Bezier curve of degree $n$ is defined by $(n+1)$ control points. The de Casteljau method is used to construct the actual curve with provided control points and parameter $t\in[0,1]$. To elaborate, de Casteljau method iteratively performs linear interpolation between each neighboring control points to get $(n-1)$ intermediate control points from $n$, then rinse and repeat, until we get the final point which is on the actual curve. In task 1, `BezierCurve::evaluateStep` only execute a single step of de Casteljau method, and was called iteratively to get the final point on curve. Scanning through enough values of $t\in[0,1]$, the output points can form a good approximation of Bezier curve. 
- Show screenshots of each step / level of the evaluation from the original control points down to the final evaluated point. <br/>

|        Initial       |        Level 1       |        Level 2       |       Level 3        |
|----------------------|----------------------|----------------------|----------------------|
| ![](images/img1.png) | ![](images/img2.png) | ![](images/img3.png) | ![](images/img4.png) |
|        Level 4       |      Final Point     |        Curve         |                      |
| ![](images/img5.png) | ![](images/img6.png) | ![](images/img7.png) |                      |
- Show a screenshot of a slightly different Bezier curve by moving the original control points around and modifying the parameter t via mouse scrolling. <br/>

|        Initial        |        Level 1        |        Level 2        |        Level 3        |
|-----------------------|-----------------------|-----------------------|-----------------------|
| ![](images/img8.png)  | ![](images/img9.png)  | ![](images/img10.png) | ![](images/img11.png) |
|        Level 4        |      Final Point      |         Curve         |                       |
| ![](images/img12.png) | ![](images/img13.png) | ![](images/img14.png) |                       |

## Task 2: Bezier Surfaces with Separable 1D de Casteljau's Subdivision
- Briefly explain how de Casteljau algorithm extends to Bezier surfaces and how you implemented it in order to evaluate Bezier surfaces. <br/>
  Using de Casteljau's method on 1D Bezier curve also applies to the calculation of 3D Bezier surfaces, albeit with some extra steps. The first step is to use 1D de Casteljau to generate Bezier curves for each row of control point (in this case, using `BezierPatch::evaluate1D`). Then we parametrized those curves with $u\in[0,1]$ and obtain intermediate control points on each curve, which can be used in `BezierPatch::evaluate1D` with parameter $v$ again to get a new curve that lies on the actual surface. 
- Show a screenshot of `bez/teapot.bez` (not .dae) evaluated by your implementation. <br/>
  ![](images/img15.png)

## Task 3: Area-Weighted Vertex Normals
- Briefly explain how you implemented the area-weighted vertex normals. <br/>
  Starting from a halfedge with current vertex as starting point, we iterate through every halfedge adjacent to current vertex with `h->twin()->next()` and accumulate the unit normal vector of its corresponding face with `h->face()->normal()`. The average of those normal vectors is the final result. 
- Show screenshots of `dae/teapot.dae` (not .bez) comparing teapot shading with and without vertex normals. <br/>
  Without vertex normals: <br/>
  ![](images/img16.png) <br/>
  With vertex normals: <br/>
  ![](images/img17.png)

## Task 4: Edge Flip
- Briefly explain how you implemented the edge flip operation and describe any interesting implementation / debugging tricks you have used. <br/>
First, I explicitly get iterators to all elements with naming convention in this picture: <br/>
![](https://cmu-graphics.github.io/Scotty3D/meshedit/local/edge_flip_diagram.png) <br/>
Then, reassign halfedge of edges/vertices/faces and updated next/twin/vertex/face/edge of halfedges. Finally return `e0`. Simple and stupid.
- Show screenshots of the teapot before and after some edge flips. <br/>
  Before: <br/>
  ![](images/img18.png) <br/>
  After: <br/>
  ![](images/img19.png) <br/>
- Write about your eventful debugging journey, if you have experienced one. <br/>
The whole process is bugless, I became paranoid when I saw some edges disappearing, it appears that it's actually overlap.

## Task 5: Edge Split
- Briefly explain how you implemented the edge split operation and describe any interesting implementation / debugging tricks you have used. <br/>
Similar to task 4, I drew a graph to make sure I do not brainf**k myself when naming elements.<br/>
  ![](images/convention.svg) <br/>
  First, I explicitly get iterators to all elements with naming convention in this picture. <br/>
  Second, I create new elements, namely `m`, `h10-15`, `e5-7`, `f2-3`. Set `e5` and `e6` `isNew` to be `true` for the purpose of T6. Set `m` `isNew` to be `true` for the purpose of T6. <br/>
  Finally, I reassign halfedge of edges/vertices/faces and updated next/twin/vertex/face/edge of halfedges. Finally return `e0`. Simple and stupid.
- Show screenshots of a mesh before and after some edge splits. <br/>
  Before: <br/>
  ![](images/img20.png) <br/>
  After: <br/>
  ![](images/img21.png) <br/>
- Show screenshots of a mesh before and after a combination of both edge splits and edge flips. <br/>
  Before: <br/>
  ![](images/img22.png) <br/>
  After: <br/>
  ![](images/img23.png) <br/>
- Write about your eventful debugging journey, if you have experienced one. <br/>
  Some half edges are not rendering correctly. I double-checked my graph and fixed some wrong vertex neighbor assignments. 
- If you have implemented support for boundary edges, show screenshots of your implementation properly handling split operations on boundary edges. <br/>
  For implementation, see branch `q5-ec`. <br/>
  ![](images/q5-ec.png) <br/>
  ![](images/q5-ec-1.png)

## Task 6: Loop Subdivision for Mesh Upsampling
- Briefly explain how you implemented the loop subdivision and describe any interesting implementation / debugging tricks you have used. <br/>
I did exactly the same thing as the comments mentioned: <br/>
1. for each existing vertex, calculate its new position based on its surrounding vertices, and set its `isNew` to false; <br/>
2. for each edge, calculate the new position of the vertex which will be created from its splitting based on four vertices mentioned in the spec, and set its `isNew` to false; <br/>
3. split all edges that does not has `isNew` set AND has two non-new vertices (that is, it's not a split edge), and set resulting new vertex to `newPosition` of the edge calculated in (2); <br/>
4. flip all edges that has `isNew` set and has one old vertex and one new vertex (use xor to calculate)
- Take some notes, as well as some screenshots, of your observations on how meshes behave after loop subdivision. What happens to sharp corners and edges? Can you reduce this effect by pre-splitting some edges? <br/>
Sharp corners and edges got smoother. This is `.\dae\icosahedron\input.dae`. <br/>
If we pre-split edges of sharp corners, the result becomes sharper. Similarly, if we pre-split other edges of sharp edges' faces, the result becomes sharper. <br/>
|Splits/Position|No|Vertex|Edge|
---|---|---|---|
0|![](images/q6-1.png)|![](images/q6-1-1.png)|![](images/q6-2-1.png)|
1|![](images/q6-2.png)|![](images/q6-1-2.png)|![](images/q6-2-2.png)|
2|![](images/q6-3.png)|![](images/q6-1-3.png)|![](images/q6-2-3.png)|
3|![](images/q6-4.png)|![](images/q6-1-4.png)|![](images/q6-2-4.png)|
- Load `dae/cube.dae`. Perform several iterations of loop subdivision on the cube. Notice that the cube becomes slightly asymmetric after repeated subdivisions. Can you pre-process the cube with edge flips and splits so that the cube subdivides symmetrically? Document these effects and explain why they occur. Also explain how your pre-processing helps alleviate the effects. <br/>
Because topologically the diagonal is not symmetric, as we subdivide and such topology is used for determining new positions of vertices, over time the overall topology "skews" that way as well. (original) <br/>
  ![](images/img30.png) <br/>
  ![](images/img31.png) <br/>
  ![](images/img32.png) <br/>
Thus if we pre-split and make the overall topology symmetric, we can get a symmetric outcome. (split symmetrically) <br/>
  ![](images/img33.png) <br/>
  ![](images/img34.png) <br/>
  ![](images/img35.png) <br/>
- If you have implemented any extra credit extensions, explain what you did and document how they work with screenshots. <br/>
I used scheme in [this paper](http://www.geometry.caltech.edu/pubs/WWTDS06.pdf) section 3.1 to calculate new boundary edge positions. Together with T5 EC, this works pretty good imo. <br/>
  ![](images/q6-beetle.png) <br/>
  ![](images/q6-beetle-1.png) <br/>

## Task 7: Design Mesh
- Save your best polygon mesh as `partsevenmodel.dae` in your docs folder and show us a screenshot of the mesh in your write-up. <br/>
[upe](images/partsevenmodel.dae)
- Include a series of screenshots showing your original mesh and your mesh after one and two rounds of subdivision. If you have used custom shaders, include screenshots of your mesh with those shaders applied as well. <br/>
![upe](images/upe.png) <br/>
![upe](images/q7-1.png) <br/>
![upe](images/q7-2.png) <br/>
![upe](images/q7-3.png) <br/>
- Describe what you have done to enhance your mesh beyond the simple humanoid mesh described in the tutorial. <br/>
I subdivided it with Cinema 4D (this is created a while ago when it was free for students), I also changed camera settings per Piazza so it doesn't screw up. 
