## Changed from the plan

Implemented the complete app in `index.html`, with inline CSS, JavaScript, raw WebGL shaders, and three embedded base64 JPEGs. There is no build step, dependency, runtime fetch, account, or API key. The initial alpine scene produces a 181 × 121 grid (21,901 vertices and 43,200 triangles) with luminance and local edge contrast driving displacement. It auto-orbits for five seconds, then returns to the initial camera. Drag, wheel, basic touch/pinch, keyboard orbit/zoom, and exact camera reset are implemented. File selection and image drop run the same processing pipeline as presets.

Added depth strength, photo/depth-map/wireframe views, keyboard accessibility, responsive layout, explicit decoding errors, and a visible source-photo fallback when WebGL is unavailable or lost. The readout reports manual camera settings; the opening orbit is a temporary animation around the zero-angle starting view, preserving a deterministic initial/reset string.

The three required scene categories use generated photorealistic images rather than camera-captured photographs. Shell image download failed because outbound DNS was unavailable; `python` and the `hermes.images` package were also unavailable. Used the built-in image generation tool for three images, converted them to 1100-pixel JPEGs with the OS image utility, and embedded all final bytes in `index.html`. No generated asset depends on its original filesystem location.

Image prompt set (built-in tool): each prompt used the prefix “Use case: photorealistic-natural. Create a landscape-format realistic photograph for a photo-to-heightfield demo. Scene: ” and suffix “. Rich detail and contrasting luminance. No text, no borders, no watermark. Save the generated asset and return its path.” The scenes were:

- “street scene: narrow sunlit European street, textured warm buildings receding in perspective, cobblestones, bright foreground facade and deep shadows”
- “landscape: rugged alpine mountains, sunlit rocky peaks, dark evergreen forest and turquoise lake in foreground”
- “close-up still life: three ripe pears in a ceramic bowl on a dark wooden table, strong side window light, textured fruit, dark background”

Validation: inspected all three final JPEGs and opened/executed the page source in a Node DOM/WebGL harness. Passed JavaScript parsing, initial visible content, nonzero canvas dimensions, three embedded JPEG headers, absence of external asset references, initial mesh generation, finite vertex data, valid index bounds, rendering calls, preset labels, drag readout changes, wheel zoom, exact reset, custom-file label, depth strength, display modes, invalid-file feedback, and context-loss feedback. The harness uses simulated canvas pixels and WebGL calls; the separate gate validates browser rendering.

## Could not do

No planned application feature was omitted. The supplied image-generation helper could not run in this environment; the built-in generator supplied the seed images instead. No objection to the plan: the UI accurately identifies the result as a luminance/edge depth illusion, not neural reconstruction.

## Omitted for lack of a service

A build-time outbound download capability for a small pretrained monocular depth-estimation model could enable an optional neural depth experiment. It was not used: the supplied plan explicitly requires the heuristic pipeline, no model download, and excludes neural depth estimation. All planned runtime functionality is service-free.
