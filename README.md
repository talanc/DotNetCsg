# DotNetCsg

DotNetCsg is a Constructive Solid Geometry (CSG) modeling library that provides 3D solid primitives and operators to manipulate them.

## History

This library is a fork of [hypar-io/Csg](https://github.com/hypar-io/Csg),
which is a fork of [praeclarum/Csg](https://github.com/praeclarum/Csg),
which is a manual port of [OpenJsCad's](https://github.com/joostn/OpenJsCad) [csg.js](https://github.com/joostn/OpenJsCad/blob/gh-pages/src/csg.js).

## Building

To build, ensure that you have the latest .NET SDK installed.

## Example

```csharp
using Csg;
using static Csg.Solids;

var wiki = Difference(
    Intersection(
        Cube(size: 1.4, center: true),
        Sphere(r: 1, center: true)
    ),
    Union(
        rod,
        rod.RotateX(90),
        rod.RotateZ(90)
    )
);

using (var fs = File.Create($"wiki.stl"))
using (var wr = new StreamWriter(fs))
{
    wiki.WriteStl("wiki", wr);
}
```

![Basic Shapes](docs/snippet-wiki.png)

## Snippets

### Namespaces

```csharp
// All types in this library are in the Csg namespace
using Csg;

// Use this for simpler calls to solids and operations
using static Csg.Solids;
```

### Basic Shapes

```csharp
// Create a basic cube
var cube = Cube(size: 1, center: true);

// Create a basic sphere
var sphere = Sphere(r: 0.5, center: true);

// Create a basic cylinder
var cylinder = Cylinder(r: 0.5, h: 1, center: true);
```

![Basic Shapes](docs/snippet-basic.png)

### Moving (Translate)

```csharp
// Create a small cube in the center
var smallCube = Cube(size: 0.4, center: true);

// Have the cube to the right (x-axis)
var moveCube = cube.Translate(x: +2);

// Have a sphere forward (y-axis)
var moveSphere = sphere.Translate(y: +2);

// Have a cylinder up (z-axis)
var moveCylinder = cylinder.Translate(z: +2);
```

![Translate](docs/snippet-translate.png)

### Operations

```csharp
var offsetSphere = sphere.Translate(x: 0.5, y: 0.5, z: 0.5);

// Union combines the shapes together, even if there's no shared area
var union = Union(cube, offsetSphere);

// Difference removes the second shape, taking the shared area from the first shape as well
var difference = Difference(cube, offsetSphere);

// Intersection keeps on the shared area of both shapes, if there's no shared area then it returns empty space
var intersection = Intersection(cube, offsetSphere);

// Note that you can also call the operations via member functions with no difference to the object returned
//var union = cube.Union(offsetSphere);
//var difference = cube.Subtract(offsetSphere);
//var intersection = cube.Intersect(offsetSphere);
```

![Basic Shapes](docs/snippet-operations.png)

### Example: Vintage Barbell

```csharp
var bar = Cylinder(r: 0.025, h: 2.1, center: true);

var weight = Sphere(r: 0.2, center: true);

// Note that we're using the same weight object more than once
// Every function that returns a Solid will not modify the original object
// So you can create your geometry and then place it in different locations

var barbell = Union(bar,
    weight.Translate(y: -0.9),
    weight.Translate(y: +0.9)
);
```

![Basic Shapes](docs/snippet-barbell.png)

### Example: Pipe

```csharp
var outer = Cylinder(r: 1, h: 4, center: true);
var inner = Cylinder(r: 0.95, h: 4, center: true);

var pipe = Difference(outer, inner);
```

![Basic Shapes](docs/snippet-pipe.png)

### Writing shape to a file

```csharp
using (var fs = File.Create($"cube.stl"))
using (var wr = new StreamWriter(fs))
//using (var wr = new BinaryWriter(fs)) // Note you can also use BinaryWriter to write as an STL binary file
{
    cube.WriteStl("cube", wr);
}
```
