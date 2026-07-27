# ASCII Globs

Render any glTF model as animated ASCII, in the browser, in a single HTML file.

Drop in a `.glb`, tune it with the panel, copy the config out. No build step, no
dependencies beyond three.js from a CDN.

## Generated using Anthropic Claude Code Opus 5
## Designed and reviewed by Illimitas Coalition. 07/26

## Run it

```bash
python3 -m http.server 8123
```

Then open <http://localhost:8123>. Any static server works — it's one file plus
a vendored Draco decoder.

## Using your own model

Upload a `.glb` with the button, or paste a URL and press Enter. Draco-compressed
files work as-is. Whatever materials and textures the file ships with are thrown
away — the renderer replaces them with its own normal-based shading, so a model
stripped of its texture graph loads faster and looks identical.

Two things commonly go wrong with other people's models, both handled
automatically:

- **No normals.** Plenty of exporters omit them. They get computed on load, and
  the geometry is welded first so the result is smooth rather than faceted.
- **Single-sided thin surfaces.** Wings, leaves, cloth. These render
  double-sided with flipped backface normals, otherwise their undersides are
  either missing or shaded as though lit from behind.

Local files are loaded through an object URL, which can't resolve sibling `.bin`
or texture files — so upload `.glb`, not `.gltf`.

## How it works

Three passes.

1. The model renders to an offscreen target, shaded from surface normals alone —
   no lights, no textures. That normal-derived luminance is the whole input to
   the effect.
2. A mouse trail draws into a quarter-resolution target.
3. A fullscreen pass pixelates the scene to a grid, reads each cell's average
   luminance, and picks a character from the ramp. Adjacent glyphs cross-fade so
   cells dissolve between characters instead of snapping.

The glyph atlas is drawn at runtime onto a canvas, so there is no image asset —
change `characters` and the atlas regenerates.

## Panel

| Control | |
|---|---|
| upload / URL | swap the model |
| spin | continuous rotation on or off; with it off, drag the model to turn it |
| scroll | what the wheel drives — see below |
| cell | glyph size in pixels; smaller reads as stipple, larger as text |
| ramp | how many characters the luminance maps onto |
| size | how large the model sits in frame |
| tone | overall brightness, which shifts where the ramp lands |
| copy config | current settings as a pasteable `CONFIG` block |

Drag rotates on both axes, with pitch clamped short of vertical. The orientation
you leave it at survives toggling spin back on.

Under **effects**: matrix rain, film grain, smear amount, melt amount, a scroll
position you can scrub directly, a button to replay the entrance, and a toggle
for the click lean described below.

### Click reactions

Click the left half of the model and it plays the stretch of its clip where the
head turns left, then eases back to wherever the scroll had it. The right half
does the same the other way. Scroll scrubbing stands down while a reaction runs,
and a fresh click cuts off one already playing.

The windows are in `CONFIG.reactions` as timecodes in seconds. The shipped
values were measured off the fox's head bone rather than eyeballed — its yaw
against the neck sits neutral, sweeps to +47 degrees left by 1.25s, returns, then
sweeps to -38 degrees right by the end of the clip:

```js
reactions: {
  left:  { from: 0.68, to: 1.25 },
  right: { from: 2.76, to: 3.42 },
},
```

Another model will want different numbers. Sample its head bone across the clip
and look for the monotonic stretches either side of neutral.

**click lean** adds a small camera-space push toward wherever you clicked, on top
of whatever the clip and scroll effects are already doing. `reactionPushAmount`
sets how far.

Keys still work and stay in sync with the panel: `m` matrix, `n` grain,
`[` `]` smear, `r` replay entrance, `e` dissolve.

### Scroll effects

| | |
|---|---|
| `animation` | scrubs the model's own glTF clip; does nothing without one |
| `orbit` | circles the camera |
| `dolly` | pulls the camera back |
| `smear` | melts rows downward into a waterfall |
| `dissolve` | lifts columns off the top, each at its own rate |
| `nothing` | ignores the wheel |

## Configuring

Everything lives in the `CONFIG` object at the top of `index.html`. The panel
writes to the same values, so the usual loop is: tune by hand, hit **copy
config**, paste the result back.

The character ramp runs darkest to densest, and the blanks in the default are
deliberate — they punch holes that keep the field airy:

```js
characters: ' * _<>,  ./O#SF +',
```

`fitRadius` and `fov` are coupled. A longer lens flattens perspective but
shrinks the frustum, so dropping `fov` means dropping `fitRadius` with it.

## Credits

The effect is a reconstruction of the ASCII treatment on
[dragonfly.xyz](https://www.dragonfly.xyz/).

`models/Fox.glb` is from the [Khronos glTF Sample Assets][khronos] collection,
CC0 1.0, originally by [PixelMannen][fox]. The Draco decoder in `libs/draco/` is
from [three.js][three], Apache-2.0.

[khronos]: https://github.com/KhronosGroup/glTF-Sample-Assets
[fox]: https://opengameart.org/content/fox-and-shiba
[three]: https://github.com/mrdoob/three.js

## License

MIT
