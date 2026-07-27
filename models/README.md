# models

`Fox.glb` — a stand-in so a fresh clone shows the loader working. Replace it by
pointing `CONFIG.model` at your own file.

Taken byte-for-byte from **Fox** in the [Khronos glTF-Sample-Assets][src]
collection, released under [CC0 1.0][cc0] (public domain, no attribution
required). Originally by [PixelMannen][orig].

One skinned mesh, 26 nodes, three animation clips — `Survey`, `Walk`, `Run`.
The loader plays `animations[0]`, so `Survey`.

Two things about this file are worth knowing if you swap in your own:

- **It has no `NORMAL` attribute** (only `POSITION`, `TEXCOORD_0`, `JOINTS_0`,
  `WEIGHTS_0`). This renderer shades entirely from normals, so the loader calls
  `computeVertexNormals()` on any geometry that arrives without them. Without
  that the shader normalises a zero vector and the model renders flat white.
- It embeds a 26 KB PNG that never gets used, because every material is replaced
  by the normal-shading pass. Left in place rather than stripped, so the file
  stays identical to upstream.

[src]: https://github.com/KhronosGroup/glTF-Sample-Assets/tree/main/Models/Fox
[cc0]: https://creativecommons.org/publicdomain/zero/1.0/
[orig]: https://opengameart.org/content/fox-and-shiba
