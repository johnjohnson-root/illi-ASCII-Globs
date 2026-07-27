# models

`avocado.gltf` / `avocado.bin` — a stand-in so a fresh clone shows the loader
working. Replace it by pointing `CONFIG.model` at your own file.

Derived from **Avocado** in the [Khronos glTF-Sample-Assets][src] collection,
released under [CC0 1.0][cc0] (public domain, no attribution required). Taken
from the `glTF-Draco` variant, so it exercises the vendored Draco decoder.

The material, texture, image and sampler graph has been stripped, and the
`material` reference removed from the mesh primitive. Geometry is unchanged.
This renderer replaces every material with its own normal-shading pass, so the
originals were only causing `GLTFLoader` to fetch three PNGs it would discard —
8.1 MB of textures for 12 KB of geometry.

[src]: https://github.com/KhronosGroup/glTF-Sample-Assets/tree/main/Models/Avocado
[cc0]: https://creativecommons.org/publicdomain/zero/1.0/
