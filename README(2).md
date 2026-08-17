# Bandline — IELTS scroll site

A single-file, frontend-only website about the IELTS test. Built around one signature element: a 3D band-score dial (Three.js) whose needle sweeps live from band 0 to band 9 as you scroll.

Open `ielts-bandline.html` directly in a browser. No build step, no server, no dependencies to install — everything loads from CDN at runtime.

## What's on the page

- **Hero** — headline, key stats, and an ambient auto-rotating 3D dial with drifting particles
- **Marquee** — infinite-scroll strip of the four skills
- **Skills grid** — four 3D flip cards (Listening, Reading, Writing, Speaking), flip on hover / tap
- **Band scale** — the signature scroll-driven section: a pinned 3D dial whose needle rotates from 0 to 9 as you scroll past each band's description
- **Study path** — a four-week timeline
- **Testimonial**
- **Signup form** — client-side email validation only, doesn't submit anywhere (no backend)

## Stack

| Purpose | Library | Loaded from |
|---|---|---|
| 3D dial, materials, lighting, shadows | [Three.js r128](https://threejs.org/) | cdnjs |
| Scroll-pinning and scroll-driven animation | [GSAP 3 + ScrollTrigger](https://gsap.com/) | cdnjs |
| Fonts | Fraunces (display), IBM Plex Sans (body), IBM Plex Mono (labels/numbers) | Google Fonts |

Everything else — the flip cards, marquee, reveal-on-scroll, nav, form — is plain CSS and vanilla JS, no framework.

## Realism techniques used for the 3D dial

- A small "studio" scene (soft light panels) is rendered through Three's `PMREMGenerator` to produce a real reflection/environment map, instead of flat-colored metal.
- `MeshPhysicalMaterial` with `clearcoat` on the gold parts for a lacquered-metal highlight on top of the base reflection.
- The dial face is a texture painted with the Canvas 2D API (radial gradient, concentric rings, hairline ticks, vignette) rather than a solid color.
- The needle is a custom `ExtrudeGeometry` blade shape with bevelled edges, not a stock cone.
- Real shadow mapping (needle/ticks cast onto the face) plus `ACESFilmicToneMapping` and sRGB output for filmic contrast.

## Customizing

Everything lives in `ielts-bandline.html`:

- **Colors / type** — CSS custom properties at the top of the `<style>` block (`--bg`, `--gold`, `--serif`, etc.)
- **Copy** — edit the HTML directly; band descriptors are in the `.scale-panel` elements, skills in `.flip-card` elements
- **Dial appearance** — `buildDial()`, `buildFaceTexture()`, and `buildStudioEnvironment()` in the `<script>` block
- **Scroll behavior** — the `ScrollTrigger.create(...)` calls near the bottom of the script

## Browser support

Needs WebGL (for the 3D dial) and a reasonably modern browser. Respects `prefers-reduced-motion` — auto-rotation and parallax are disabled if the user has that set, though the scroll-driven dial rotation in the band-scale section still tracks scroll position directly since it's functional, not decorative. Falls back gracefully on narrow viewports: fewer particles, single-column grids, no hover-flip hint (replaced by a tap hint).

## Known limitations

- No backend — the signup form only validates and shows a confirmation message locally.
- Band descriptors are original paraphrases, not official IELTS wording.
- Two independent Three.js scenes run (hero + band-scale); on low-end mobile devices this is the main performance cost if you plan to extend the page with more 3D elements.
