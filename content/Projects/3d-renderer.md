---
title: 3D Rotations C++ 
draft: false 
tags: [C++, SDL2,  Linear Algebra]
---

# 3D Rotations in C++ from scratch (with SDL2)
>**GOAL** Build a 3D renderer in C++ from scratch inspired by [Javidx9 olcGameEngine](https:/$/youtu.be/ih20l3pJoeU?si=vdKEuT0bbfRhcwy4)

>**WHY** To demystify the magic behind linear matrix transformations by having maths and computer science work together to create graphics.

>**REPO** [GitHub Repository](https://github.com/player0-glitch/Vector3D)
--- 

## High-level Structure
- [Math for 3D Rotations](#math-for-3D-rotations)
- [Implementation in C++](#Implementation-in-C++)
- [Skills Demonstrated](#Skills-Demonstrated)
- [Future Implementations](#Future-Implementations)
---

## Math for 3D Rotations
Linear Algebra is the foundation of 3D graphics. The key concepts included in my implementation 
that can be found real life game engines are:

### [Perspective Projection](https://en.wikipedia.org/wiki/3D_projection#:~:text=puzzle%2Dlike%20fashion.-,Perspective%20projection,-%5Bedit%5D)
This projects points from a 3D space onto a 2D plane, this allows us to simulate depth and perspective in our 3D world.

$$
\begin{bmatrix}
\frac{1}{\text{aspectRation}\cdot\tan(\text{fovRad})} & 0 & 0 & 0 \\
0 & \frac{1}{\tan(\text{fovRad})} & 0 & 0 \\
0 & 0 & \frac{-\text{Far+Near}}{\text{far} - \text{near}} & \frac{-2\cdot\text{(Far*Near)}}{\text{Far-Near}}\\
0 & 0 & -1 & 0\\
\end{bmatrix}
$$

$$
aspectRation = \frac{\text{width}}{\text{height}}
$$
$$
fovRad=\frac{\text{fovAngleDeg}\cdot\pi}{180}
$$

$$
Far = 1000\\
Near = 0.1 
$$
### Rotation Matrices
These matrices are used to rotate points in 3D space around the X, Y, and Z axes. They are fundamental for transforming the orientation of objects in 3D space. These matrices can be combined together using matrix multiplication. However, I've opted to have these matrices separate for clarity[^1].

$$
R_x(\theta) = \begin{bmatrix}
1 & 0 & 0 & 0\\
0 & \cos(\theta) & -\sin(\theta) & 0  \\
0 & \sin(\theta) & \cos(\theta) & 0\\
0 & 0 & 0 & 1\\
\end{bmatrix}
$$

$$
R_z(\theta)= \begin{bmatrix}
\cos(\theta) & -\sin(\theta) & 0 & 0\\
\sin(\theta) & \cos(\theta) & 0 & 0\\
0 & 0 & 1 & 0\\
0 & 0 & 0 & 1\\
\end{bmatrix}
$$
*The 1 in the fourth column is used to keep our w-component intact during matrix multiplication of rotational matrices*

[^1]: The rotational matrix around Y axis has not yet been implemented.

## Implementation in C++

### Core Components 
Implemented my own vector, triangle and matrix structs as these are the back bones of the mathematics that will be used. The w-component in the vector struct is used to scale/normalize the vector during transformations, allowing for homogeneous coordinates which are essential for 3D graphics.

```cpp
struct V3D {
    float x=0, y=0, z=0;
    float w=1; // Homogeneous coordinate for 3D transformations

    V3D(float x,float y, float z) {
      x=X;
      y=Y;
      z=Z;
      w=1.0f;
    }
};

```

Triangle code snippet:
```cpp
struct Triangle {
  V3D p1; //{0, 0, 0};
  V3D p2; //{0, 0, 0};
  V3D p3; //{0, 0, 0};

  // hold our actual cartesian points of our object
  std::array<V3D, 3> points;

  // Constructor
  Triangle(struct V3D P1, struct V3D P2, struct V3D P3) {
    p1 = P1;
    p2 = P2;
    p3 = P3;
    points.at(0) = p1;
    points.at(1) = p2;
    points.at(2) = p3;
  }
``` 

### Rendering Pipeline

Project all the points from 3D space onto the screen space, apply rotations around the z-axis as a predefined angled. Further rotate the transformed points around the x-axis to simulate a 3D rotation effect. The angles of rotation between the z and x axis are different as to avoid Gimbal lock. Lastly I scale the points from unit vectors that are now between -1 and 1 to the screen space of the window or in this case a viewport of size 620 x 820.

Used SDL2 for rendering the pixels to the screen and simple frame buffers. This allowed for me to setup a simple visualisation of the 3D rotations and transformations.

I achieved frame delayed rendering manually by using ```SDL_GetPerformanceCounter()``` as well as ```SDL_Delay()``` . This gave me the ability to control the frame rate of the rendering loop, allowing for smoother 60 fps rendering and animations.

### C++ Features Used
-RAII for resource safety and automatic cleanup of resources, I did not want to focus on manual memory management as that was not the goal of this project.
- std::vector for dynamic arrays to hold triangles and points. I do not make use of the std::vector's ability to grow dynamically however, range based for-loops are a pleasure to write.
- No optimisation flags were set, however all warnings were turned on with the -Wall flag.

## Skills Demonstrated
As this was a maths based computer programme, it deepened my understanding of 3D maths and linear matrix transformations. Taking on this project has improved my proficiency in C++ with the use of initialiser lists. I've also gotten more comfortable with operator overloading. My debug skills have gotten much better I feel like I could be a detective. 
Made use of ```perf record -g``` and ```perf report``` to profile the code and identify bottlenecks in the rendering pipeline. This helped me understand the performance implications of my code and how to optimize it for better frame rates. This insight helped me restructure my code as the rendering calls were expensive and many.

![Performance record](/Projects/images/perf_record.png)
*Perf record of the update function, where most of the maths happens, before fixing rendering call issues. (13.44%)*

![Performance report](Projects/images/perf_report.png)
*Perf record after updating calls to the renderer in the update function but just delivering the data to the renderer. (2.88%)*

## Future Implementations
- [ ] Implement interactivity where angles of rotations can be changed in real-time using ImGUI.
- [ ] Allow changing distance from camera (allow user to zoom in and out).
- [ ] Add more 3D objects to choose from because why not?


### Contacts
email: [mokhaditlhalefo@gmail.com](mailto:mokhaditlhalefo@gmail.com)

LinkedIn: [linkedin.com/in/mokhaditlhalefo](https://www.linkedin.com/in/tlhalefo-mokhadi-445b05226/)

