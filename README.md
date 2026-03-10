# StampForge v2

**Browser-based stamp designer with STL export, G-code slicing, and USB direct printing.**

Draw a design, configure your printer, export STL or G-code — or send directly to the printer via USB. Single HTML file, zero dependencies, works offline (except USB print which requires localhost).

![StampForge](https://img.shields.io/badge/version-2.0-orange) ![License](https://img.shields.io/badge/license-MIT-green)

---

## What it does

StampForge lets you design fabric stamps directly in the browser and take them all the way to print — no installation, no slicer software required.

```
Draw → Export SVG   (laser / vinyl cutter)
     → Export STL   (open in any slicer)
     → Export GCODE (ready to print)
     → Print USB    (send directly to printer)
```

---

## Features

### Drawing tools
- **Pencil** — freehand stroke
- **Line, Rectangle, Ellipse** — geometric shapes, stroke or filled
- **Fill** — flood fill
- **Eraser** — paint-over with background colour
- **Text** — place text with system fonts
- Undo / Redo / Clear / Import image
- **Symmetry axes** — ⇔ left/right, ⇕ top/bottom, or both simultaneously
- **Inline project name** — click "untitled" on the canvas to rename; all exports inherit the name

### Export pipeline
```
Canvas pixels
  → Binary mask  (morphological closing — fills antialiasing gaps)
  → Boundary edge tracing
  → Douglas-Peucker simplification  (configurable epsilon)
  → Chaikin smoothing  (0–8 passes, configurable)
  → Outer / hole classification  (even-odd, handles complex shapes)
  → earcut triangulation  (2.2.4, inlined — no CDN)
  → Watertight STL: shared vertices across top, bottom, side walls
```

### Print settings
| Parameter | Default | Description |
|---|---|---|
| Width / Height (mm) | 60 × 60 | Physical stamp dimensions |
| Resolution | 600px | Canvas resolution |
| Layer Height | 0.20 mm | Slice thickness |
| Nozzle Size | 0.40 mm | Extrusion width reference |
| Base Thickness | 1.2 mm | Solid base plate |
| Design Layers | 2 | Raised stamp surface |
| Mirror for stamp | ✓ | Flip X for correct impression |
| Include base plate | ✓ | Add solid base to model |
| Simplify (px) | 0.8 | Douglas-Peucker epsilon |
| Curve smooth | 3 passes | Chaikin subdivision |

### Printer profiles (18 included)
| Brand | Models |
|---|---|
| Prusa | i3 MK3S/MK3S+, MK4, MINI/MINI+ |
| Creality | Ender-3 / V2, Ender-3 S1 / S1 Pro, CR-10, CR-10 V2/V3 |
| Bambu Lab | P1P, P1S, X1, X1C, A1, A1 Mini |
| Artillery | Sidewinder X1/X2, Genius Pro |
| Voron | 2.4, Trident |
| Anycubic | Kobra, Kobra 2 |
| Elegoo | Neptune 3, Neptune 4 |
| Longer | LK4 Pro (Klipper) |
| Generic | RepRap / Custom |

Selecting a printer auto-fills: nozzle temp, bed temp, print/travel/first-layer speeds, retraction, filament diameter.

### G-code engine
- Perimeter paths from smooth vector contours
- Rectilinear infill via scanline ray-casting (alternating direction per layer)
- Correct extrusion calculation: `E = (length × layerH × extW) / filArea`
- Retraction / unretraction between moves
- Auto-centred on printer bed
- Start / end G-code per printer profile with `{nozzle_temp}`, `{bed_temp}` placeholders
- Cura `.json` / `.cfg` profile import

### USB direct print (Web Serial API)
- Connect directly to printer USB — no drivers beyond the printer's USB stack
- Line-by-line transmission with `ok` handshake (Marlin flow control)
- Live log with colour-coded responses (ok / error / info)
- Progress bar + ETA
- Emergency cancel (`M112`)
- Port released automatically when modal closes

---

## Usage

```bash
git clone https://github.com/your-username/stampforge.git
cd stampforge

# For USB printing — Web Serial requires a secure context:
python3 -m http.server 8080
# open http://localhost:8080/stampforge.html

# For everything else (draw, STL, SVG, GCODE):
open stampforge.html   # file:// works fine
```

---

## Keyboard shortcuts

| Key | Tool |
|---|---|
| `P` | Pencil |
| `L` | Line |
| `R` | Rectangle stroke |
| `Shift+R` | Rectangle filled |
| `E` | Ellipse stroke |
| `Shift+E` | Ellipse filled |
| `F` | Fill |
| `X` | Eraser |
| `T` | Text |
| `Ctrl+Z` | Undo |
| `Ctrl+Y` | Redo |
| `Delete` | Clear canvas |

---

## File structure

```
stampforge.html   ← entire application (~95KB, single file)
README.md
```

No build step. No package.json. No node_modules.
earcut 2.2.4 is inlined — zero network requests.

---

## USB print requirements

- Chrome or Edge (Firefox does not support Web Serial API)
- Serve via `localhost` for Web Serial to be available — not `file://`
- Close any other app using the port (Repetier-Host, PrusaSlicer, Pronterface) before connecting

---

## Recommended materials for fabric stamps

| Material | Notes |
|---|---|
| TPU 95A | Best ink transfer, flexible, durable |
| Flexible PLA | Good alternative, easier to print |
| Standard PLA | Works, less flexible — good for firm stamps |

Suggested: 100% infill on stamp face, print face-down for best surface quality.

---

## Known limitations (v2)

- Text tool does not mirror with symmetry axes
- USB print tested with Marlin firmware — Klipper via serial should work but is untested
- No layer preview before export
- Undo history clears on canvas resize

---

## Roadmap (v3)

- [ ] Repetier-Server API integration (send to print queue over network)
- [ ] Layer-by-layer preview
- [ ] Tile / repeat pattern mode
- [ ] Import SVG as drawing layer
- [ ] Save / load project state

---

## License

MIT — do whatever you want with it.

---

*Built with Claude — Vibe Coding session, March 2026.*
