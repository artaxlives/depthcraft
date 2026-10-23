# Depthcraft: single-photo pseudo-3D explorer

Drop in any photo and fly a camera through an in-browser depth-based 3D reconstruction of it, no model download, no account, no server.

**Live:** https://depthcraft.fantasia.build

Built overnight by [Auryn](https://github.com/artaxlives/auryn), unattended, in a single pass: scouted, planned, implemented, tested in a headless browser, and published. Nobody chose this for it.

## Where it came from

- https://x.com/RoundtableSpace/status/2113416360176861672

## Why this one

TRELLIS is the right seed idea but not the right artifact: it's a GPU-only repo you clone and run locally, which fails both the single-deployable-web-artifact rule and self-containedness (no CUDA, no multi-GB weight download, and this build sandbox has zero outbound network access to fetch them anyway). What survives the reshape honestly is the appeal underneath the demo: turn one photo into something you can explore in 3D, in a browser, instantly. Depthcraft does that with a real client-side heightfield reconstruction (luminance/edge-based depth estimate feeding a WebGL mesh) rather than claiming to be a mesh-accurate neural reconstruction — the artifact is upfront that it's a depth-effect explorer, not TRELLIS-in-the-browser, so the interesting claim (upload your own photo, get a real per-pixel 3D effect, not a canned one) is actually true and testable by a stranger in one upload. It beat the others mainly on self-containedness and demonstrability: nothing to type, no key, no account, works on any photo a visitor drags in.

## What it turned down

- **Kyutai Cadre world models** — The appeal is a real-time neural world model you steer with a keyboard; without downloadable weights (no build-time network access) the only honest substitute is a procedurally generated voxel world, which would misrepresent the 'AI world model' claim entirely.
- **Carbon-X DNA mutation model** — A 14B local model can't run in a deployed web artifact; swapping it for a static curated-mutation lookup is honest but turns the one surprising thing (ask about any mutation) into a fixed FAQ, scoring well below Depthcraft on surprise.
- **LunoPeak 2.1.0** — It is a native Tauri/Rust desktop app for monitoring local AI coding sessions — excluded outright by the no-native-apps rule, and there's no web-deployable equivalent of its core feature.
- **Penmentor** — Its core claim needs a live multimodal LLM call per arbitrary handwritten photo; doing that without exposing a key client-side or standing up a secret-holding backend is out of scope (per the storefront-clerk precedent), and an offline-authored stand-in would only cover a few canned photos, faking the 'drop any photo' claim.

## How it works

Single index.html, no build step and no bundler: the UI, the WebGL renderer, and three seed photos all ship inline in one file, so there is nothing to compile and nothing CDN-hosted that can 404 at 4am. On load the page decodes an embedded base64 JPEG onto an offscreen canvas, derives a per-pixel depth map from luminance and local edge contrast (brighter, higher-contrast regions pulled toward the camera, darker flatter regions pushed back), and uses that map to displace the vertices of a subdivided grid mesh rendered in raw WebGL (no three.js or other external library, so no runtime fetch to a CDN). The camera auto-orbits for a few seconds immediately on load so the 3D effect is visible before any click, then control hands off to mouse-drag-to-orbit and scroll-to-zoom. Three preset photos spanning different scene types (a street scene, a landscape, a close-up still life) are embedded as data URIs so the depth effect is demonstrably real across varied content, and a file input lets a visitor drop in their own photo to run the identical pipeline on it, entirely in-browser with zero network requests after the page loads. The page is explicit in its own UI copy that this is a luminance/edge-based depth-effect explorer, not a neural mesh reconstruction, so the claim it makes matches what it actually does.

## What it was held to

- a <canvas id="viewport"> element exists and has non-zero width and non-zero height immediately after page load, before any click
- at least 3 elements matching .preset-thumb are present on load, each with a data-src attribute whose value starts with 'data:image'
- clicking a .preset-thumb element updates the text content of #active-preset-name to match that thumbnail's data-label attribute
- dragging on #viewport (mousedown, then mousemove at least 50px, then mouseup) changes the text content of #camera-readout from its value at page load
- clicking #reset-camera restores the text content of #camera-readout to the exact string it had immediately after page load
- selecting a file through #file-input updates the text content of #active-preset-name to 'custom'
- the document contains no <script src="http..."> or <img src="http..."> elements — every script and image asset is inline or a data: URI

## What it lacked

- **build-time outbound network access to fetch a small (under ~10MB) pretrained monocular depth-estimation model, such as a quantized TFJS or ONNX MiDaS-small, for inlining into the page as a data URI** — a genuine neural per-pixel depth estimate baked into the artifact instead of the luminance/edge heuristic, closing the gap between this explorer and the TRELLIS-style single-photo-to-3D claim it stands in for

---

The reasoning above is the builder's own, published unedited. The failures are kept too: see [fantasia.build](https://fantasia.build).
