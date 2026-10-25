# DEWGLASS

> *Where dew meets reflection.*
> **BREATHE · TOUCH · REVEAL**

A real-time liquid mirror for your webcam. Condense digital fog with natural facial gestures, erase with fingertip precision, and clear swaths with a wave of your fist.

Designed with an Apple Liquid Glass visual language, featuring authentic handwritten signature engraving, tactile press-and-hold ambient condensation, and pure on-device computer vision.

- **Breathe or blow** toward your webcam to condense digital fog naturally around your mouth.
- **Pinch** thumb and index finger together to etch finger-sized lines (fixed 10 px) through the condensation.
- **Make a fist** and sweep across the mirror to wipe away broad swaths like clearing a steamy glass with your sleeve.
- **Press & hold Ambient Fog** to gradually accumulate natural condensation across the mirror surface, which gently evaporates over time.

Built as a lightweight, single-page web application with no build steps, bundlers, or heavy frameworks.

---

## Getting Started

Browsers require a **secure context** (`https://` or `http://localhost`) to grant camera access. Opening `index.html` directly via `file://` will be blocked by browser security policies.

### Local Development Server

Run a lightweight static HTTP server from this directory:

```bash
# Python 3
python3 -m http.server 8000

# or Node.js / npx
npx serve .
```

Open <http://localhost:8000> in your browser and click **Enter the Mirror**.

*Note: An active internet connection is required on initial load to fetch the pinned MediaPipe tracking models from the CDN. After initial caching, models run entirely in your local browser.*

---

## Controls & Shortcuts

| Input | Control | Action |
|---|---|---|
| **Pursed-lip blowing** | Webcam Facial Gesture | Blooms realistic condensation around mouth |
| **Press & hold button** | Dock `#btnAmbient` | Gradually builds ambient fog; stops on release |
| **Hold <kbd>A</kbd>** | Keyboard shortcut | Keyboard press-and-hold for ambient fog |
| **Hold <kbd>Space</kbd>** | Keyboard shortcut | Manual breath override (ideal for silent testing) |
| **Pinch** (thumb + index) | Hand Landmark Tracking | Draws through fog with fixed 10 px fingertip precision |
| **Closed Fist** | Hand Landmark Tracking | Wipes away a broad swath of condensation |
| **<kbd>C</kbd>** / Clear button | Dock `#btnClear` | Clears all fog and drawings |
| **<kbd>D</kbd>** / HUD button | Dock `#btnHud` | Toggles on-screen diagnostics (FPS, mouth aspect, ratios) |
| **<kbd>S</kbd>** / Shutter button | Dock `#shutter` | Captures and downloads a high-resolution PNG snapshot |
| **<kbd>Esc</kbd>** / Exit button | Dock `#btnExit` | Cleanly exits mirror and returns to welcome screen |

---

## Privacy & Security

- **Webcam-Only:** The application requests only video access (`{ video: ... }`). Microphone permission is **never** requested or accessed.
- **100% On-Device:** All landmark detection (MediaPipe FaceMesh and Hands) and canvas compositing run locally on your device via WebAssembly/WebGL. No video frames, audio, or canvas data ever leave your machine.
- **No Analytics / Telemetry:** Zero external tracking, telemetry scripts, or remote storage.
- **Pinned Dependencies:** External tracking libraries are pinned to specific CDN versions with `crossorigin="anonymous"`:
  - `@mediapipe/hands@0.4.1675469240`
  - `@mediapipe/face_mesh@0.4.1633559619`

---

## Architecture & How It Works

The mirror pipeline is rendered continuously through a multi-layer 2D canvas compositing stack:

1. **Live Camera Feed:** Mirrored horizontally and rendered to the base view canvas.
2. **Off-Screen Fog Alpha Mask (`fogC`):** An off-screen canvas tracking condensation density per pixel. Blowing and ambient plumes add alpha via `source-over`; fingertip drawing and fist wiping carve transparent glass using `destination-out`.
3. **Blurred Frost Layer (`frostC` / `smallC`):** The camera frame is downsampled to 25% resolution, blurred, brightened, and tinted with soft white vapor tones. It is masked by `fogC` via `destination-in` and composited onto the mirror.
4. **Micro-Droplets (`dropC`):** Clinging condensation beads and trickling droplets with specular highlights, masked by the active fog layer so water drops appear only where condensation exists.
5. **Floating Liquid Glass Dock:** Translucent, glassmorphic floating control dock with `-webkit-backdrop-filter` blur, specular top-edge highlights, and tactile pointer events.

---

## Static Deployment

The project consists solely of static assets:
- `index.html` — Application entry point, styling, and logic
- `assets/engraving.png` — Stylized mirror engraving asset

Because all internal asset paths are relative (`assets/engraving.png`), the application can be hosted at the root of a domain or within any subpath on any static hosting provider (GitHub Pages, Cloudflare Pages, Netlify, Vercel, or an S3/Nginx bucket). Remote deployments require **HTTPS** for browser camera permissions.
