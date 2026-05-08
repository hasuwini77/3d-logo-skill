# 3D Logo Skill

A Claude Code skill that turns any logo image into a premium 3D spinning coin/medal with chrome edges and environment reflections.

## What it does

Give it any logo (PNG, JPG) and it generates a self-contained React component that:

- Renders the logo as a 3D spinning coin
- Auto-removes dark/black backgrounds
- Extracts the logo's exact outline for the chrome rim
- Both sides show the logo with readable text
- Environment reflections for a premium chrome finish
- Smooth Y-axis rotation

## Install

```bash
claude install github:hasuwini77/3d-logo-skill
```

## Usage

In Claude Code, just say:

```
Use /3d-logo on my logo at public/logo.png
```

Or:

```
Make my logo 3D and spinning
```

## Dependencies

The target project needs:

```bash
npm install three @react-three/fiber @react-three/drei
npm install -D @types/three
```

## Environment Presets

Choose the reflection style:

| Preset | Look |
|--------|------|
| `studio` | Clean, professional chrome |
| `warehouse` | Industrial, gritty |
| `city` | Urban, bright reflections |
| `night` | Dark, moody, tactical |
| `dawn` | Soft, warm |
| `sunset` | Amber warmth |

## How it works

1. Loads the logo image and converts dark pixels to transparent (runtime canvas manipulation)
2. Scans the alpha channel row-by-row to extract the exact perimeter of the visible content
3. Builds two PlaneGeometry faces (front + back) with the transparent texture
4. Constructs a custom BufferGeometry rim that follows the extracted outline
5. Applies chrome material with environment reflections
6. Wraps everything in a spinning group with configurable speed

## License

MIT
