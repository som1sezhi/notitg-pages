# Texture padding (WIP)

## NPOT textures

NotITG likes textures with dimensions that are powers of 2 (128x128, 512x256, etc.). If you try to load an image with dimensions that are Not Powers of Two (NPOT), NotITG will pad out the dimensions to the next highest powers of two.

For example, here is a 200x150 image:

![cool texture](cool_texture.png)

If loaded into NotITG as a sprite texture, it gets padded out to 256x256:

![cool texture with black padding on bottom and right](cool_texture_padded.png)

(The padding here is colored black for visualization purposes. In actuality it may be transparent, or it may contain garbage data.)

## Texture coordinates vs. image coordinates

This behavior gives rise to a distinction between *texture size* and *image size*, and between *texture coordinates* and *image coordinates*:

![diagram visualizing texture and image coordinates on the cool texture](cool_texture_diagram.png)

- The **texture size** is the size of the whole texture, including the padding. The dimensions will always be powers of 2. In the above example, the texture size would be 256x256.
- The **image size** is the size of the image without padding. In the above example, the image size would be 200x150.
- With **texture coordinates**, values in the range [0, 1] span over the whole texture, including the padding. In the above example, (1, 1) would refer to the *very* bottom-right corner of the texture, deep in the padding, while the bottom-right corner of the actual image would be located at (200/256, 150/256), or (0.78125, 0.5859375).
- With **image coordinates**, values in the range [0, 1] span only over the image within the texture, excluding the padding. In the above example, (1, 1) would refer to the bottom-right corner of the actual image.

In Lua, you can grab the texture/image dimensions by calling the `GetTextureWidth()`/`GetImageWidth()` and `GetTextureHeight()`/`GetImageHeight()` methods on a `RageTexture`.

In shaders, the texture and image sizes are available through the uniforms `textureSize` and `imageSize`. Additionally, in the fragment shader, the texture/image coordinates are available through the varying variables `textureCoord` and `imageCoord`.

## Issues with NPOT textures

If you actually create a sprite using this texture, though, you may notice that the image displays correctly, without any padding visible. NotITG sets the sprite's texture

- AFTs are padded with black
- examples with customtexturerect
- need to take padding into account when assigning texture coordinates to sprites, polygons
- best solution is to just use power-of-2 textures when possible

- may not always be possible, e.g. AFTs
- use img2tex to convert

## NPOT textures on models?

- NPOT textures are resized on models
- allows original texture coordinates to be used
- imageSize, textureSize, (imageCoord) not passed in, youll have to pass it in yourself

![](cool_texture_stretched.png)