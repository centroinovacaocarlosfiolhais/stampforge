# StampForge v1

**Browser-based stamp designer with STL export for 3D printing.**

Draw a design, configure your print settings, export a watertight STL ready to slice — no installation required. Single HTML file, zero dependencies, works offline.

![StampForge](https://img.shields.io/badge/version-1.0-orange) ![License](https://img.shields.io/badge/license-MIT-green)

---

## What it does

StampForge lets you design fabric stamps directly in the browser and export them as print-ready 3D models. The workflow is:

1. Draw on the canvas using the built-in tools
2. Adjust physical dimensions and print parameters
3. Export SVG (for laser cutting / vinyl) or STL (for 3D printing)

The exported STL is a **watertight extruded solid** — not two floating planes. It slices correctly in Cura, PrusaSlicer, and Repetier-Host.

---

## Features

### Drawing tools
- **Pencil** — freehand stroke
- **Line, Rectangle, Ellipse** — geometric shapes (stroke or filled)
- **Fill** — flood fill
- **Eraser** — paint-over with background colour
- **Text** — place text with system fonts
- Undo / Redo / Clear
- Import background image

### Symmetry
- **⇔ Vertical axis** — left/right mirror while drawing
- **⇕ Horizontal axis** — top/bottom mirror while drawing
- Both axes simultaneously for 4-quadrant symmetry

### Export pipeline
```
Canvas pixels
  → Binary mask (morphological closing to fill antialiasing gaps)
  → Boundary edge tracing
  → Douglas-Peucker simplification
  → Chaikin smoothing (0–8 passes, configurable)
  → Outer / hole classification (even-odd rule)
  → earcut triangulation (handles holes correctly)
  → Watertight STL: top face + bottom face + side walls sharing exact vertices
```

### Print settings
| Parameter | Default | Description |
|---|---|---|
| Width / Height (mm) | 60 × 60 | Physical stamp dimensions |
| Resolution | 600px | Canvas resolution |
| Layer Height | 0.20 mm | From slicer profile |
| Nozzle Size | 0.40 mm | From slicer profile |
| Base Thickness | 1.2 mm | Solid base plate height |
| Design Layers | 2 | Raised stamp surface layers |
| Mirror for stamp | ✓ | Flip X so impression reads correctly |
| Include base plate | ✓ | Add solid base to STL |
| Simplify (px) | 0.8 | Douglas-Peucker epsilon |
| Curve smooth | 3 passes | Chaikin subdivision passes |

### Cura profile import
Drag and drop or load a `.json` (Cura 4+) or `.cfg` / `.ini` profile to automatically populate layer height and nozzle size.

---

## Usage

Open `stampforge.html` in any modern browser. No server required.

```bash
# Clone and open
git clone https://github.com/your-username/stampforge.git
open stampforge/stampforge.html
```

Or just drop the file into a browser tab.

---

## Keyboard shortcuts

| Key | Tool |
|---|---|
| `P` | Pencil |
| `L` | Line |
| `R` | Rectangle (stroke) |
| `Shift+R` | Rectangle (filled) |
| `E` | Ellipse (stroke) |
| `Shift+E` | Ellipse (filled) |
| `F` | Fill |
| `X` | Eraser |
| `T` | Text |
| `Ctrl+Z` | Undo |
| `Ctrl+Y` | Redo |
| `Delete` | Clear canvas |

---

## File structure

```
stampforge.html   ← entire application (single file, ~68KB)
README.md
```

The earcut 2.2.4 triangulation library is inlined directly — no CDN, no network requests.

---

## Recommended print settings (fabric stamps)

- **Material:** TPU 95A or flexible PLA for best ink transfer
- **Layer height:** 0.20 mm
- **Nozzle:** 0.40 mm
- **Design height:** 2 layers = 0.40 mm raised surface
- **Infill:** 100% for the stamp face
- **Orientation:** print face-down for best surface quality

---

## Known limitations (v1)

- Text tool does not mirror with symmetry axes (rasterised on commit)
- No G-code export (planned for v2)
- Undo history is cleared on canvas resize

---

## Roadmap (v2)

- [ ] G-code export via local slicer integration (Repetier-Server API / CuraEngine)
- [ ] Layer preview before export
- [ ] Pattern / tile repeat mode
- [ ] Import SVG as drawing

---

## License

MIT — do whatever you want with it.

---

*Built with Claude — Vibe Coding session, March 2026.*
