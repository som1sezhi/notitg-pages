# Shader variables

NotITG automatically passes values into some select uniform variable names for use in your shaders. The default vertex shader also passes some select varying variables into the fragment shader. These variables are listed below.
## Varyings

```glsl
varying vec3 position;
varying vec3 normal;
varying vec4 color;
varying vec2 textureCoord;
varying vec2 imageCoord;
```

- `position`: The position of this point in local space.
- `normal`: The normal vector at this point, in view space. Not normalized.
- `color`: The vertex color, often set via `Actor:diffuse()` and friends.
- `textureCoord`: The texture coordinates at this point. This can be plugged
  into `texture2D()` directly.
- `imageCoord`: The image coordinates at this point.

For more info on `textureCoord` vs. `imageCoord`, see [[Texture padding]].

## Uniforms

```glsl
uniform float time;
uniform float beat;
uniform vec2 resolution;
uniform vec2 textureSize;
uniform vec2 imageSize;
uniform mat4 modelMatrix;
uniform mat4 viewMatrix;
uniform mat4 projectionMatrix;
uniform mat4 textureMatrix;
uniform sampler2D sampler0;
uniform sampler2D sampler1;
```

- `time`: The time into the song, in seconds.
- `beat`: The time into the chart, in beats.
- `resolution`: The resolution of the screen. Basically equivalent to  `vec2(SCREEN_WIDTH, SCREEN_HEIGHT)`.
- `textureSize`: The size of the actor's main texture.
- `imageSize`: The actual size of the image contained within the actor's main texture.
- `modelMatrix`: See the section on [[#Transformation matrices]].
- `viewMatrix`: See the section on [[#Transformation matrices]].
- `projectionMatrix`: See the section on [[#Transformation matrices]].
- `textureMatrix`: Matrix that transforms texture coordinates before they are passed to the fragment shader.
- `sampler0`: The actor's main texture.
- `sampler1`: The texture loaded in a model's alphamap slot (see [[Spheremaps]]).

For more info on `textureSize` vs. `imageSize`, see [[Texture padding]].

TODO: investigate `isEnvMap`?

### Additional playfield shader uniforms

The game passes some additional uniforms into playfield shaders:

```glsl
uniform int iCol;
uniform int iPlayfield;
uniform int isHold;
uniform int isReceptor;
uniform float fYOffset;
uniform float fNoteBeat;
```

- `iCol`: The playfield column (0-indexed).
- `iPlayfield`: The playfield number (0-7 for P1-P8).
- `isHold`: 1 for holds, 0 for all else.
- `isReceptor`: 1 for receptors, 0 for all else.
- `fYOffset`: (arrow shaders only) The y-offset of the arrow along the arrow path,
  in "pixels". Beware that the location of the zero point is affected by mods
  and is not usually located at the receptors.
- `fNoteBeat`: (arrow and hold shaders only) Which beat the arrow lies on. For
  holds, which beat the hold starts on.

## Transformation matrices

In most 3D games, vertex coordinates are transformed to a series of coordinate systems in order to determine where the vertices should end up on screen:

- **Local space** specifies coordinates relative to the object's origin, before any transformations are applied to the object.
- **World space** specifies coordinates relative to the world's origin.
- **View space** specifies coordinates relative to the camera's point of view.
- **Clip space** specifies coordinates in terms of the screen/viewport, kind of. In clip space, points with coordinates between -1 and 1 will be visible on screen, and points outside this range will be clipped (discarded).

These transformations are performed using a series of transformation matrices:

- The **model matrix** transforms local space to world space, essentially positioning and transforming the object to its place in the world.
- The **view matrix** transforms world space to view space. You can think of it as positioning the camera in the world, though it's really more like transforming the whole world to move stuff into the camera's view.
- The **projection matrix** transforms view space to clip space. Taking the effects of perspective into account, it compresses all coordinates that the camera can see (those within the camera's [view frustum](https://en.wikipedia.org/wiki/Viewing_frustum)) into a box between (-1, -1, -1) and (1, 1, 1) so these coordinates can be easily clipped.

The `modelMatrix`, `viewMatrix`, and `projectionMatrix` uniforms that NotITG passes into shaders work pretty much like this, though some aspects of their behaviors may be a little unusual:

- `modelMatrix` includes all transformations applied to the actor and its containing ActorFrames (but not any of the FOV-related stuff). This basically transforms the coordinates to "almost-view" space.
- If the actor has no FOV applied, `viewMatrix` is just the identity matrix (the transformation does nothing). If FOV is applied, `viewMatrix` translates the x- and y-coordinates by `-resolution / 2.0` (presumably to move the origin point to the center of the screen). It also performs a translation in the negative z direction, pushing the actor further into the depth axis, with the translation becoming more extreme as the FOV decreases (probably to counteract any apparent size changes caused by changing the FOV).
- `projectionMatrix` seems to work pretty much as usual. Just make sure to name the uniform `projectionMatrix` and NOT `perspectiveMatrix`. Unlike what documentation on the Discord might indicate, NotITG doesn't pass anything into the `perspectiveMatrix` uniform.