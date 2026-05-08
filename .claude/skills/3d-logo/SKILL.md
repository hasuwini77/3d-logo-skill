---
name: 3d-logo
description: Create a 3D spinning coin/medal effect from any logo image using React Three Fiber. Auto-detects the logo's outline, removes dark backgrounds, builds a chrome-rimmed coin that follows the exact logo shape, and adds environment reflections. Use when the user wants a 3D logo, spinning logo, rotating logo, coin effect, medal effect, logo animation, or mentions making a logo "3D" or "spin." Also trigger when user says "3d-logo", "spinning coin", "logo coin", "3D badge", or wants to turn a flat image into a premium 3D rotating element.
---

# 3D Logo Spinner

Generate a self-contained React component that renders any logo as a premium 3D spinning coin/medal with chrome edges and environment reflections.

## What it produces

A `SpinningLogo3D.tsx` component that:
- Takes any logo image (PNG, JPG, SVG rasterized) and renders it as a 3D coin
- Auto-removes dark/black backgrounds by converting to transparency at runtime
- Extracts the logo's exact perimeter from the alpha channel
- Builds a chrome rim that follows the logo's actual outline shape
- Renders the logo readable on BOTH sides of the coin
- Adds environment reflections for a premium chrome finish
- Spins smoothly on the Y axis

## Dependencies

The target project needs these packages:
```bash
npm install three @react-three/fiber @react-three/drei
npm install -D @types/three
```

## How to build the component

### Step 1: Identify the logo

Ask the user for the logo path (relative to `public/` or `src/assets/`). Check the file exists. Read it to see the logo visually — this helps you understand the shape.

Check the actual file format:
```bash
file <path-to-logo>
```
The extension may lie (e.g., a `.png` that's actually JPEG). This matters because JPEG has no alpha channel — the transparency must be generated at runtime.

### Step 2: Generate the component

Create `SpinningLogo3D.tsx` using this architecture:

#### 2a. Dynamic transparency generation

The logo image likely has a dark/black background with no alpha channel. Convert dark pixels to transparent at runtime using an offscreen canvas:

```tsx
function useTransparentTexture(logoPath: string, threshold = 18) {
  const srcTexture = useTexture(logoPath)
  return useMemo(() => {
    const img = srcTexture.image as HTMLImageElement
    const canvas = document.createElement('canvas')
    canvas.width = img.naturalWidth || img.width
    canvas.height = img.naturalHeight || img.height
    const ctx = canvas.getContext('2d')!
    ctx.drawImage(img, 0, 0, canvas.width, canvas.height)
    const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height)
    const d = imageData.data
    for (let i = 0; i < d.length; i += 4) {
      const brightness = (d[i] + d[i + 1] + d[i + 2]) / 3
      if (brightness < threshold) d[i + 3] = 0
    }
    ctx.putImageData(imageData, 0, 0)
    const tex = new CanvasTexture(canvas)
    tex.colorSpace = SRGBColorSpace
    return tex
  }, [srcTexture, threshold])
}
```

If the logo already has a transparent background (actual PNG with alpha), skip the brightness threshold and just use the existing alpha. You can detect this: if `file` reports "PNG image data" (not JPEG), the image may have native transparency. Still apply the same hook but check alpha instead of brightness.

#### 2b. Perimeter extraction

Scan the alpha channel row-by-row to find left and right edges of the opaque region. This traces the exact outline of the logo:

```tsx
function extractPerimeter(data: Uint8ClampedArray, width: number, height: number) {
  const rightEdge: [number, number][] = []
  const leftEdge: [number, number][] = []
  for (let y = 0; y < height; y++) {
    let left = -1, right = -1
    for (let x = 0; x < width; x++) {
      if (data[(y * width + x) * 4 + 3] > 0) {
        if (left === -1) left = x
        right = x
      }
    }
    if (left !== -1 && right > left) {
      rightEdge.push([right, y])
      leftEdge.push([left, y])
    }
  }
  // Right edge top→bottom, then left edge bottom→top = closed perimeter
  const raw = [...rightEdge, ...leftEdge.reverse()]
  // Simplify to ~400 vertices
  const step = Math.max(1, Math.floor(raw.length / 400))
  return raw.filter((_, i) => i % step === 0).map(([px, py]) => [
    px / width - 0.5,
    0.5 - py / height,
  ] as [number, number])
}
```

#### 2c. Rim geometry builder

Build a quad strip connecting front edge (z=+half) to back edge (z=-half) around the perimeter. Use `DoubleSide` rendering to avoid normal-direction issues:

```tsx
function buildRim(outline: [number, number][], planeSize: number, thickness: number) {
  const half = thickness / 2
  const s = planeSize
  const n = outline.length
  const positions: number[] = []
  const normals: number[] = []
  for (let i = 0; i < n; i++) {
    const i2 = (i + 1) % n
    const x1 = outline[i][0] * s, y1 = outline[i][1] * s
    const x2 = outline[i2][0] * s, y2 = outline[i2][1] * s
    const ex = x2 - x1, ey = y2 - y1
    const len = Math.sqrt(ex * ex + ey * ey) || 1
    const nx = ey / len, ny = -ex / len
    positions.push(
      x1, y1, half, x1, y1, -half, x2, y2, half,
      x1, y1, -half, x2, y2, -half, x2, y2, half,
    )
    normals.push(nx, ny, 0, nx, ny, 0, nx, ny, 0, nx, ny, 0, nx, ny, 0, nx, ny, 0)
  }
  const geo = new BufferGeometry()
  geo.setAttribute('position', new Float32BufferAttribute(positions, 3))
  geo.setAttribute('normal', new Float32BufferAttribute(normals, 3))
  return geo
}
```

#### 2d. Coin assembly

Two `PlaneGeometry` faces (front + back) with the transparent texture. The back face is rotated `[0, PI, 0]` — because PlaneGeometry has correct UV mapping by default, BOTH faces show the logo text readable (no mirroring).

**Critical**: Do NOT flip UV coordinates. PlaneGeometry UVs are already correct. The Y rotation on the back face handles the mirror naturally.

```tsx
function Coin() {
  const groupRef = useRef<Group>(null)
  const { texture, rimGeometry } = useLogoAssets()
  useFrame((_, delta) => {
    if (groupRef.current) groupRef.current.rotation.y += delta * SPIN_SPEED
  })
  const half = THICKNESS / 2
  return (
    <group ref={groupRef}>
      <mesh position={[0, 0, half]}>
        <planeGeometry args={[PLANE_SIZE, PLANE_SIZE]} />
        <meshBasicMaterial map={texture} side={FrontSide} transparent depthWrite={false} />
      </mesh>
      <mesh position={[0, 0, -half]} rotation={[0, Math.PI, 0]}>
        <planeGeometry args={[PLANE_SIZE, PLANE_SIZE]} />
        <meshBasicMaterial map={texture} side={FrontSide} transparent depthWrite={false} />
      </mesh>
      <mesh geometry={rimGeometry}>
        <meshStandardMaterial
          color="#8ecae6" metalness={1.0} roughness={0.03}
          emissive="#06b6d4" emissiveIntensity={0.15}
          envMapIntensity={1.5} side={DoubleSide}
        />
      </mesh>
    </group>
  )
}
```

#### 2e. Canvas and lighting

```tsx
export function SpinningLogo3D({ size = 540 }: { size?: number }) {
  return (
    <div style={{ width: size, height: size }} className="mx-auto">
      <Canvas
        camera={{ position: [0, 0, 7], fov: 40 }}
        gl={{ alpha: true, antialias: true, powerPreference: 'high-performance', preserveDrawingBuffer: true }}
        dpr={[1, 2]}
      >
        <ambientLight intensity={0.8} />
        <directionalLight position={[5, 5, 5]} intensity={2.5} />
        <directionalLight position={[-3, -2, 4]} intensity={0.8} color="#06b6d4" />
        <directionalLight position={[0, 0, -5]} intensity={0.5} color="#0ea5e9" />
        <Environment preset="warehouse" />
        <Suspense fallback={null}>
          <Coin />
        </Suspense>
      </Canvas>
    </div>
  )
}
```

### Step 3: Environment preset

Ask the user which reflection environment they want:
- **"studio"** — clean, professional, neutral chrome
- **"warehouse"** — industrial, gritty, darker
- **"city"** — urban reflections, bright
- **"night"** — dark, moody, elegant
- **"dawn"** — soft, warm
- **"sunset"** — amber warmth

Default to "studio" if the user doesn't specify.

### Step 4: Tunable constants

Place these at the top of the file so the user can easily adjust:

| Constant | Default | What it controls |
|----------|---------|-----------------|
| `PLANE_SIZE` | 4.8 | Size of the logo face in 3D units |
| `THICKNESS` | 0.45 | Coin edge thickness |
| `SPIN_SPEED` | 0.35 | Rotation speed (radians/sec) |
| `BG_THRESHOLD` | 18 | Brightness cutoff for background removal (0-255) |

### Step 5: Integration

Wrap the component in `<Suspense>` when used — the texture loading suspends inside the R3F Canvas, which is handled internally:

```tsx
// Just drop it in — Suspense is handled inside the Canvas
<SpinningLogo3D size={540} />
```

## Common pitfalls

- **DO NOT use CircleGeometry** for the face — its UV mapping mirrors the texture. Always use PlaneGeometry.
- **DO NOT flip UVs** — PlaneGeometry UVs are correct by default. Flipping causes mirrored text.
- **Suspense MUST be inside `<Canvas>`** — R3F's `useTexture` suspends within its own reconciler. An outer Suspense won't catch it and the component will flash/disappear.
- **Use `DoubleSide` on the rim material** — the perimeter winding creates mixed normal directions. DoubleSide ensures all faces render regardless.
- **Use `depthWrite={false}`** on the transparent face materials — prevents z-fighting between front and back faces during rotation.
- **Check the actual file format** — `.png` files are sometimes JPEG internally. JPEG has no alpha, so transparency must always be generated from brightness.

## Rim color customization

The chrome rim color should complement the logo. For most logos:
- Cyan/blue logos → `color="#8ecae6"` with `emissive="#06b6d4"` (default)
- Gold/warm logos → `color="#e6c88e"` with `emissive="#d4a506"`
- Red logos → `color="#e68e8e"` with `emissive="#d40606"`
- Green logos → `color="#8ee6ae"` with `emissive="#06d46a"`
- Neutral/white logos → `color="#c0c0c0"` with `emissive="#888888"`

Match the rim to the logo's dominant accent color for a cohesive look.
