<div align="center">

```
╒═╗╔╦╗   ╦  ╔═╗╔═╗╔═╗   ╔═╗╦╔═╦╦  ╦
 ═╣ ║║───║  ║ ║║ ╦║ ║───╚═╗╠╩╗║║  ║
╘═╝═╩╝   ╩═╝╚═╝╚═╝╚═╝   ╚═╝╩ ╩╩╩═╝╩═╝
```

**Turn any flat logo into a premium 3D spinning coin.**\
One command. Any logo. Instant 3D.

<br/>

<img src="screenshots/logo1.gif" width="280" alt="Phoenix Shield 3D coin" />&nbsp;&nbsp;&nbsp;<img src="screenshots/logo2.gif" width="280" alt="Cosmic Eye 3D coin" />&nbsp;&nbsp;&nbsp;<img src="screenshots/logo3.gif" width="280" alt="Wolf Compass 3D coin" />

<sub><b>Phoenix Shield</b> &nbsp;&bull;&nbsp; <b>Cosmic Eye</b> &nbsp;&bull;&nbsp; <b>Wolf Compass</b></sub>

<br/><br/>

[![Agent Skill](https://img.shields.io/badge/Agent_Skill-compatible-00C853?style=for-the-badge)](https://agentskills.io)&nbsp;
[![Claude Code](https://img.shields.io/badge/Claude_Code-6C5CE7?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0id2hpdGUiPjxwYXRoIGQ9Ik0xMiAyQzYuNDggMiAyIDYuNDggMiAxMnM0LjQ4IDEwIDEwIDEwIDEwLTQuNDggMTAtMTBTMTcuNTIgMiAxMiAyem0wIDE4Yy00LjQyIDAtOC0zLjU4LTgtOHMzLjU4LTggOC04IDggMy41OCA4IDgtMy41OCA4LTggOHoiLz48L3N2Zz4=)](https://github.com/hasuwini77/3d-logo-skill)&nbsp;
[![Cursor](https://img.shields.io/badge/Cursor-000?style=for-the-badge)](https://cursor.com)&nbsp;
[![Copilot](https://img.shields.io/badge/GitHub_Copilot-000?style=for-the-badge&logo=githubcopilot&logoColor=white)](https://github.com/features/copilot)&nbsp;
[![Gemini CLI](https://img.shields.io/badge/Gemini_CLI-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://github.com/google-gemini/gemini-cli)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](LICENSE)
[![React Three Fiber](https://img.shields.io/badge/R3F-Three.js-black?style=flat-square&logo=threedotjs)](https://github.com/pmndrs/react-three-fiber)

</div>

---

> The rim isn't a generic circle — it traces your logo's actual outline pixel by pixel.

## Install

Works with any AI coding agent that supports the [Agent Skills specification](https://agentskills.io) — Claude Code, Cursor, Codex, Gemini CLI, GitHub Copilot, and [50+ more](https://agentskills.io/specification).

```bash
npx skills add hasuwini77/3d-logo-skill
```

<details>
<summary>Agent-specific install</summary>

```bash
# Claude Code
claude install github:hasuwini77/3d-logo-skill

# Cursor / Copilot / Gemini CLI / others
npx skills add hasuwini77/3d-logo-skill
```

The `npx skills add` command auto-detects which agents you have installed and places the skill in the correct directory for each.

</details>

## Usage

Once installed, just ask your agent:

```
Make my logo at public/logo.png into a 3D spinning coin
```
```
Use /3d-logo on src/assets/brand-logo.png with night reflections
```
```
Turn my brand logo into a spinning 3D medal with sunset reflections
```

The skill generates a self-contained `SpinningLogo3D.tsx` component. Drop it anywhere in your React app:

```tsx
import { SpinningLogo3D } from './SpinningLogo3D'

<SpinningLogo3D size={540} />
```

## Environment Presets

| Preset | Best for | Feeling |
|--------|----------|---------|
| **studio** | Corporate, SaaS | Clean, professional |
| **warehouse** | Gaming, industrial | Gritty, textured |
| **city** | Tech, startups | Urban, dynamic |
| **night** | Dark themes, premium | Moody, elegant |
| **dawn** | Health, wellness | Soft, warm |
| **sunset** | Creative, entertainment | Rich amber |

## How It Works

1. **Background removal** — dark pixels converted to transparent via offscreen canvas
2. **Perimeter extraction** — alpha channel scanned row-by-row to trace the logo outline (~800 vertices)
3. **Face rendering** — two `PlaneGeometry` faces with correct UVs (readable on both sides)
4. **Chrome rim** — indexed `BufferGeometry` ring with tangent-derived outward normals for smooth reflections
5. **Reflections** — `MeshStandardMaterial` with `metalness: 1.0, roughness: 0.12` plus environment map for polished chrome

## Customization

| Constant | Default | Controls |
|----------|---------|----------|
| `PLANE_SIZE` | 4.8 | Logo face size (3D units) |
| `THICKNESS` | 0.45 | Coin edge thickness |
| `SPIN_SPEED` | 0.35 | Rotation speed (rad/s) |
| `BG_THRESHOLD` | 18 | Background removal sensitivity (0–255) |

## Requirements

```bash
npm install three @react-three/fiber @react-three/drei
npm install -D @types/three
```

## Contributing

1. Fork &rarr; feature branch &rarr; edit `.claude/skills/3d-logo/SKILL.md`
2. Test locally with `claude install .`
3. Open a PR with before/after examples

Ideas: more presets, hover interactions, glow effects, shape detection improvements.

## License

MIT &mdash; do whatever you want with it.

---

<div align="center">
<sub>Built with <a href="https://github.com/pmndrs/react-three-fiber">React Three Fiber</a> + <a href="https://github.com/pmndrs/drei">drei</a> + <a href="https://threejs.org">Three.js</a></sub>
<br/><br/>
<b>If this skill saved you time, drop a ⭐</b>
</div>
