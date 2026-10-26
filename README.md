# Cutting Table

A browser-based tool that turns a 2D quilt piecing pattern (DXF) into 3D-printable seam-allowance templates — no installs, no server, everything runs client-side in `index.html`.

## What it does

1. **Parses a DXF piecing pattern.** Reads raw `LINE`/`LWPOLYLINE`/`POLYLINE` geometry, reconstructs the closed pieces with a planar face-tracing algorithm (even when the source file has no explicit piece boundaries — just a flat arrangement of line segments), and groups congruent pieces by matching side lengths and interior angles.
2. **Adds a seam allowance.** Computes a true mitered outward offset (0.25" by default) around each unique piece shape, with automatic beveling for very sharp corners.
3. **Exports templates** in several formats, one per unique piece size:
   - **DXF (R12)** and **SVG** — cut line + stitch line on one layer per piece, so Shapr3D (or any CAD tool) can select and extrude the seam-allowance ring in one step.
   - **STL** and **STEP** — a real 4mm-thick 3D solid of just the seam-allowance ring (the material between the cut line and the sew line), ready to 3D print as a physical template.
   - **PDF** — pieces laid out and tiled across A4/Letter pages at true scale, with a 1" calibration line on every page for print-scale verification.

## Using it

Open `index.html` in a browser. Drop in your own DXF, or click **Load Sample (Storm at Sea)** to try it with the bundled 15"×15", 28-piece, 8-unique-size "Storm at Sea" pattern. Click **About / History** in the header for a short write-up of how each feature came about.

## How it was built

This app was built iteratively with [Claude](https://claude.ai), working directly from a real pattern file and real import failures (e.g. debugging why Shapr3D rejected the first DXF export) rather than guesswork. Every generated file format has been validated against an independent tool, not just visual inspection:

- STL meshes checked with `trimesh` (watertight, consistent winding, correct Euler number and volume)
- STEP solids checked structurally and by importing into a real OpenCascade kernel via `cadquery`
- PDFs checked with `pymupdf` and pixel-level image inspection

See the in-app **About / History** panel for the full development timeline.

## Status

Single-file app (`index.html`), no build step, no dependencies beyond what's bundled inline (including [`earcut`](https://github.com/mapbox/earcut) for triangulation, MIT/ISC licensed).
