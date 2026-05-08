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

<!-- TODO: Add a GIF/video of a spinning logo here -->
<!-- Record a 10-second screen capture and convert to GIF -->
<!-- See "Adding Examples" section below -->

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

### Startup & SaaS Logos
Clean tech logos, app icons, brand marks. The chrome rim gives them instant premium feel.

### Gaming & Esports Logos
Shield-shaped logos, mascots, badges. The chrome rim follows the shield outline perfectly.

### Sports Team Crests
Shield crests, circular emblems. The 3D depth makes them feel like real championship coins.

### Agency & Studio Logos
Wordmarks, monograms, abstract shapes. Any silhouette works.

### Open Source Project Logos
Hexagonal, circular, or custom-shaped. The perimeter extraction handles any silhouette.

---

## Environment Presets

Choose the reflection vibe that matches your brand:

| Preset | Best for | Feeling |
|--------|----------|---------|
| **studio** | Corporate, SaaS, portfolios | Clean, professional, neutral chrome |
| **warehouse** | Gaming, industrial, edgy brands | Gritty, dark, textured |
| **city** | Tech, startups, modern apps | Urban, bright, dynamic |
| **night** | Dark themes, premium brands | Moody, elegant, sleek |
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

- **More environment presets** - Custom HDR environments (space, underwater, neon)
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

## Adding Examples

Want to add example screenshots/GIFs to this README? Here's how:

### Where to find free logos to demo

1. **[SVGRepo](https://www.svgrepo.com)** - Free SVG logos. Download as PNG or screenshot at high res
2. **[Logopond](https://logopond.com)** - Logo inspiration (check individual licenses)
3. **[Brandfetch](https://brandfetch.com)** - Brand logos (for demo purposes)
4. **Your own logos** - Best option! Create a quick logo with [Canva](https://canva.com) or [Figma](https://figma.com)
5. **AI-generated logos** - Use any AI image generator to create a logo specifically for the demo

### What makes a good demo logo

- **Dark background** (black or near-black) - the skill removes it automatically
- **Colorful / glowing edges** - looks stunning as a 3D coin
- **Clear silhouette** - shields, circles, badges, crests work best
- **High resolution** (512x512 or 1024x1024) - more detail in the rim

### How to capture the demo

1. Run the skill on a logo in a test React project
2. Open the page in Chrome
3. Use a screen recorder (OBS, ShareX, or Chrome's built-in recorder)
4. Capture a 5-10 second loop of the coin spinning
5. Convert to GIF with [ezgif.com](https://ezgif.com) or `ffmpeg -i video.mp4 -vf "fps=15,scale=600:-1" demo.gif`
6. Add to `screenshots/` folder and reference in this README

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
