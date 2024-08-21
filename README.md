# UnityTutorials

This repository serves as a record of my exploration into computer graphics rendering techniques following:

Jasper Flicks:
https://catlikecoding.com/unity/tutorials/

This readme will serve as a summary of my main takeaways from each tutorial.

### Basics1:

The first tutorial focuses on learning the basic UI and navigation of Unity, adding new objects to the scene and how to properly setup it's heirachy.
Some basic graphic concepts are covered such as creating materials and settings its albedo.
Animating objects is also briefly covered, through writing scripts which are added to scene objects as components. This serves as a way to inject custom behaviour to the scene. Exposing Object manipulation is done by manipulating its `Transform` attribute, which is set and exposed to the script by attaching `[SerializeField]` to the `Transform` variable in the script E.g.

```
[SerializeField]
Transform exampleVar
```

Which exposes exampleVar as a field to any objects with this script attached.
In the tutorial, the object is rotated by manipulating the `localRotation` of the object. Many other fields can be manipulated as detailed from:
https://docs.unity3d.com/ScriptReference/Transform.html

### Basics2:

This tutorial introduces `prefabricated` objects which are template objects. Changes made to the prefab object are reflected on the instances of it.
To instantiate a prefab in a script, `Instantiate(<prefabObject>)` method is called. This method returns a copy of the object in the parameter.

Two different rendering pipelines in unity, BRP and URP.

Using time as part of the function, we are able to animate the graph

### Basics3:

Using parametric equations, we are able to animate more complex graphs in 3 dimensions. This is related to more complex graphics techniques, where the graph points represent vertexes of a mesh.

### Basics4:

Understanding performance is crucial to optimizing. Unity supports game window statistics in the unity editor, showing framerate and frame render times. Frame debugger, breaksdown the different APIs called to render the frame. The unity profiler, gives a more detailed breakdown of performance metrics behind each frame. Using it on a build versus editor window run, will have different results.

Basic UI creation with scripting to switch between framerate and framerender time was also covered.

Linear interpolation or lerp between graph functions, make for decent transistions between equations.

### Basics5:

The key concept of real time rendering, we want to minimize the number of times data is transfered between the CPU and GPU, as well as to minimize per pixel work/calculations done on the CPU, instead doing it on the GPU which handles that work better.

Achieved through the GPU buffer. Assign a chunk of the GPU memory to store data used in rendering.
\*Important to release any buffers created at the end to explicitly release GPU resources.

Compute Shaders, is a shader stage used for computing information used in other stages of the render pipeline. In this tutorial, it is used to calculate the graph positions for every frame.

Macros help reduce the number of repeated code when defining kernel functions.
\*Spacing when defining these code chunks need to be carefully checked e.g.
`KERNEL_FUNCTION(function)` and `KERNEL_FUNCTION (function)`
have the difference of a compile error in the latter and a successful compile.

### Basics6:

Fractals, creating objects with self similarity. Good case study on how to optimize or avoid recursive work.
Creation of the Sierpiński triangle, faces performance issues at depths higher than 6 without any optimizations. Further complicated when animating the objects.
Unity struggles with recursive hierachy. Hence solution would be to remove the recursive nature.
By using a flat hierachy, we are able to use procedural rendering on parts of the same level, reducing the overhead of thousands of game objects

Further optimization can be accomplished in unity using its job system. Similar systems can be found/manually programmed in other engines to handle parallel computations. The system takes advantage of the CPU's multiple cores and special SIMD(single-instruction-multiple-data) instructions. Examples of SIMD are vectorization, where individual instructions on an array index can be ran parallel on multiple indexes by replacing it with instructions on vectors.
E.g.:`data[i] = 2f * data[i]` vectorized, the array is treated as a single vector and multiplied.

### Basics7:

Interpolating between Colors, achieved in unity `Color.lerp(<firstCol>, <secondCol>, <t>)` where t clamps between 0 and 1, `@t = 0` color is `firstCol`, `@t = 1` color is `secondCol`. To get configurable gradients, use a serializeField of type `Gradient` and set materialPropertyBlock color with `gradient.Evaluate(t)` where t is the same function as above.

Using the instance identifiers and applying it to a function, in this tutorial, modulo 5. Results in a pseudo random scatter of colors in the fractal parts. This however falls prey to tiling at higher depth levels and columns of repeated color at the lower depth values.

To get rid of this, add different offset per level to the sequence or even use different sequence per level.

### Mesh1:

Meshes consists of triangles and vertices. Which side a triangle is visible from is determined by the orientation of its vertex indices. By default, if they are arranged in a `clockwise `direction the triangle is considered to be forward-facing and visible. `Counter-clockwise `triangles are discarded.

UV coordinates are [0-1] how the texture/albedo map behaves on a mesh depends on its settings. clamping or wrapping, its tiling setting and whether the uv coordinates have been set up correctly.

Normal maps are textures with normal vector values encoded as colors. Normal maps help create fake details on surfaces. This helps in performance, where instead of a high geometry object, such as fully rendered dirt, we can apply a normal map to simulate the bumps on a flat mesh plane. Tangent vectors are also required when using normal maps to properly orient the generated bumps from the normal map.

### Mesh2:

Creating 3 objects, such as cubes. Care must be taken when setting triangles for the mesh. Often multiple methods are required, the way the vertices are created and indexed also play a part in how the creation of the meshes is carried out.

Manipulating the normals of the object, can also change its shape. Often more complex geometry can be represented by mathematical formulas.
In the case of a rounded cube as described in the tutorial:
`For any point on the outer cube there's a point closest to it on the inner cube. We can use this to determine the normal vectors for the rounded cube.`

Meshes, can be split into submeshes to aid in texture application. In the tutorial, the cube is split into three pairs of opposite faces.

### Mesh3:

Continue iterating over till a satisfiable result is met. Perfect solutions might not exist and there will be trade offs with each change.
In this tutorial, converting the rounded cube mesh from Mesh2 to a sphere. The naive approach first introduced is to normalize the vertexes from the square to the center origin and the multiply it by the radius. This has the effect of stretching the center of the meshes as a result of not distributing the vertices equally around the circumfrence of the sphere.
The second approach,
