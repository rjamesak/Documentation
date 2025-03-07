---
title: Decals
image: 
description: Learn how use decals in Babylon.js.
keywords: diving deeper, meshes, decals
further-reading:
    - title: Basic Shapes
      url: /features/featuresDeepDive/mesh/creation/set
video-overview:
video-content:
---

## How to Use Decals

These are usually used to add details on meshes (bullets hole, local details, etc...), a decal is a mesh produced from a subset of a previous one with a small offset in order to appear on top of it.

Creation Example :
```javascript
var decal = BABYLON.MeshBuilder.CreateDecal("decal", mesh,  {position: myPos}, scene);
```
Don't forget the _mesh_ parameter; this is the mesh to which the decal is applied.

Properties, all optional :

property|value|default value
--------|-----|-------------
position|_(Vector3)_ position of the decal (World coordinates) | (0, 0, 0)
normal|_(Vector3)_  the normal of the mesh where the decal is applied onto (World coordinates)|Vector3.Up
size|_(Vector3)_  the x, y, z sizes of the decal|(1, 1, 1)
angle|_(number)_ the angle to rotate the decal|0

<Playground id="#1BAPRM#73" title="Simple Example of Decals" description="Simple example of pasting decals in a Babylon.js scene."/> click on the cat.

Starting with v5.28.0, decals can be created for rigged meshes and two new options are available:

property|value|default value
--------|-----|-------------
localMode|defines that the computations should be done with the local mesh coordinates instead of the world space coordinates. Use this mode if you want the decal to be parented to the source mesh and move/rotate with it. | false
<nobr>cullBackFaces</nobr>|defines if the back faces should be removed from the decal mesh|false

<br/>
Note that `localMode=true` is automatically enforced for rigged meshes (when `sourceMesh.skeleton !== null`) otherwise it would not work. Also, you should probably always set `localMode` to `true` even for non rigged meshes as it will allow your decal to follow the source mesh even if this mesh moves/rotates.

The `cullBackFaces` option set to `true` will make sure the faces that are back facing the direction of projection (the inverse direction of the `normal` option) won't be created. It can help you avoid leaking a texture in the back of an object if your decal size is too big in the z dimension (`size.z`).

For eg:

![Projection box](/img/features/decals/decal_projbox.jpg)

In this picture, the red box materializes the projection box and has the `size` dimensions passed in `options`.

As the projection box extends farther from the back, with `cullBackFaces=false` the projected texture will be visible on the back:

front|back
-----|----
![Front](/img/features/decals/decal_front.jpg!350)|![Back](/img/features/decals/decal_back_nok.jpg!350)

<br/>
With `cullBackFaces=true` however, the projected texture will not be visible on the back:

front|back
-----|----
![Front](/img/features/decals/decal_front.jpg!350)|![Back](/img/features/decals/decal_back_ok.jpg!300)

<br/>
Of course, you can try to lower the dimensions of the projection box:

![Projection box](/img/features/decals/decal_projbox_small.jpg)

It will work even with `cullBackFaces=false` as the box does not extends past the back. But even this reduced box is too big if we want to create a decal on the arm, for example. For the arm, you would need a very tiny box, but then this tiny box may be too tiny if we project on another part of the body... So, you would need to adjust the size depending on where you create the decal, which can be tedious and difficult to achieve in practice. Using `cullBackFaces=true` with a relatively big box will work in all cases without worrying about where the decal is created.

You can use this PG to experiment with decals: <Playground id="#EEUVTY#199" title="Decals with rigged and moving meshes" description="Example of pasting decals for rigged meshes."/>

You can move over the dude / cube / mesh and left click to create a permanent decal. The "Cull back faces of decal" checkbox lets you experiment with the `cullBackFaces` option.

Note that the decal is created with `localMode=true`, that's why it works as expected for the sphere and the box (the decal will follow the rotation/movement of the mesh).
