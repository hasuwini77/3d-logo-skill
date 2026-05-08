# 3D Logo Skill for Claude Code

**Turn any flat logo into a premium 3D spinning coin with chrome edges and environment reflections.**

One command. Any logo. Zero 3D modeling experience needed.

---

## Demo

```
You: "Use /3d-logo on public/logo.png"

Claude: *generates a self-contained SpinningLogo3D.tsx component*
        *auto-detects outline, removes background, builds chrome rim*
        *logo spins as a shiny 3D coin in your React app*
```

### What it looks like

| Front face | Edge profile | Back face |
|:---:|:---:|:---:|
| Your logo, crisp and clear | Chrome rim follows your logo's exact shape | Logo readable on both sides |

> The rim isn't a generic circle. It traces your logo's actual outline pixel by pixel.

---

## Install

```bash
claude install github:hasuwini77/3d-logo-skill
```

That's it. The skill is now available in Claude Code.

## Usage

Just tell Claude what you want:

```
Make my logo at public/logo.png into a 3D spinning coin
```

```
Use /3d-logo on src/assets/brand-logo.png with warehouse reflections
```

```
Create a 3D spinning medal from this company logo
```

Claude generates a drop-in `SpinningLogo3D.tsx` component you can use anywhere in your React app:

```tsx
import { SpinningLogo3D } from './SpinningLogo3D'

function Hero() {
  return (
    <div className="flex justify-center py-12">
      <SpinningLogo3D size={540} />
    </div>
  )
}
```

---

## Examples That Work Great

### Gaming / Esports Logos
Shield-shaped logos, mascots, badges. The chrome rim follows the shield outline perfectly.

### Company Badges
Round or rectangular company logos. Dark backgrounds get auto-removed; the rim adapts to any shape.

### Open Source Project Logos
Hexagonal, circular, or custom-shaped. The perimeter extraction handles any silhouette.

### Military / Tactical Emblems
Eagles, stars, crossed swords. The coin effect adds a medal-like premium feel.

### Sports Team Crests
Shield crests, circular emblems. The 3D depth makes them feel like real championship coins.

---

## Environment Presets

Choose the reflection vibe that matches your brand:

| Preset | Best for | Feeling |
|--------|----------|---------|
| **studio** | Corporate, SaaS, portfolios | Clean, professional, neutral chrome |
| **warehouse** | Gaming, industrial, tactical | Gritty, dark, textured |
| **city** | Tech, startups, modern apps | Urban, bright, dynamic |
| **night** | Military, security, dark themes | Moody, tactical, stealth |
| **dawn** | Health, wellness, calm brands | Soft, warm, inviting |
| **sunset** | Creative, entertainment | Rich amber tones |

```
"Use /3d-logo on my logo with night reflections"
```

---

## How It Works Under the Hood

1. **Background removal** - Loads the image into an offscreen canvas, scans every pixel, converts dark pixels (brightness < threshold) to transparent. Works even on JPEGs pretending to be PNGs.

2. **Perimeter extraction** - Scans the alpha channel row-by-row to find the leftmost and rightmost opaque pixel per row. Traces the full outline as a polygon with ~400 vertices.

3. **Face rendering** - Two `PlaneGeometry` faces (front + back) with the transparent texture. PlaneGeometry UVs are naturally correct, so text reads properly on both sides.

4. **Chrome rim** - Custom `BufferGeometry` built as a quad strip connecting front-edge to back-edge around the perimeter. `DoubleSide` rendering ensures full coverage.

5. **Environment reflections** - drei's `<Environment>` component provides HDR reflections. Combined with `metalness: 1.0, roughness: 0.03`, the rim looks like polished chrome.

---

## Customizable Constants

| Constant | Default | What it does |
|----------|---------|-------------|
| `PLANE_SIZE` | 4.8 | Logo face size in 3D units |
| `THICKNESS` | 0.45 | Coin edge thickness |
| `SPIN_SPEED` | 0.35 | Rotation speed (rad/s) |
| `BG_THRESHOLD` | 18 | Background removal sensitivity (0-255) |

---

## Requirements

Your project needs React and these packages:

```bash
npm install three @react-three/fiber @react-three/drei
npm install -D @types/three
```

---

## Contributing

PRs welcome! Some ideas for contributions:

- **More environment presets** - Custom HDR environments (military, space, underwater)
- **Interaction** - Hover to pause/speed up, click to flip, drag to rotate manually
- **Glow effects** - Post-processing bloom around the rim
- **Multiple logos** - Carousel of spinning coins
- **Performance** - `frameloop="demand"` option for static pages
- **Shape detection** - Better algorithms for complex outlines (concave shapes, holes)
- **Export** - Generate a video/GIF of the spinning logo

### How to contribute

1. Fork this repo
2. Create a feature branch
3. Edit `.claude/skills/3d-logo/SKILL.md`
4. Test with `claude install .` in the repo root
5. Open a PR with before/after examples

---

## Tech Stack

Built with:
- [React Three Fiber](https://github.com/pmndrs/react-three-fiber) - React renderer for Three.js
- [drei](https://github.com/pmndrs/drei) - Useful helpers for R3F
- [Three.js](https://threejs.org/) - 3D library

---

## License

MIT - do whatever you want with it.

---

**If this skill saved you time, drop a star!**
