# MINRECRAFT — Voxel Sandbox Engine

A lightweight browser-based voxel engine built with **:contentReference[oaicite:0]{index=0}** and vanilla JavaScript.

---

## 📌 Overview

**MINRECRAFT** is a fully self-contained voxel sandbox engine running in the browser.  
It features:

- Infinite procedural terrain (chunk-based)
- Real-time meshing
- Block breaking & placing
- Collision & gravity system
- Inventory + 2×2 crafting
- Save / Load via `localStorage`
- Fly mode
- Dynamic chunk streaming
- Custom Perlin-based terrain generation

No build tools. No backend. No frameworks.  
Just one HTML file and a CDN for Three.js.

---

# 🌍 World System

## Chunk Configuration

| Property        | Value    |
| --------------- | -------- |
| Chunk Size      | 32 × 32  |
| Chunk Height    | 64       |
| Render Distance | 4 chunks |
| Sea Level       | 18       |

Chunks are stored in:

```
Map<string, Chunk>
key format → "cx|cz"
```

Each chunk contains:

```
{
  data: Uint8Array,
  cx: number,
  cz: number
}
```

### Block Storage

Blocks are stored in a flat array:

```
index = lx + lz * CS + ly * CS * CS
```

This ensures:

- Cache efficiency
- Minimal memory overhead
- Fast lookup

---

# 🌱 Procedural Terrain

Terrain generation uses:

- Custom 2D Perlin Noise
- Fractal Brownian Motion (fBM)
- Layered height logic

### Terrain Layers

```
Bottom        → Stone
Underground   → Stone
Sub-surface   → Dirt
Surface       → Grass or Sand
```

Trees spawn probabilistically above sea level.

---

# 🧱 Block System

### Block IDs

| ID  | Block  |
| --- | ------ |
| 0   | Air    |
| 1   | Grass  |
| 2   | Dirt   |
| 3   | Stone  |
| 4   | Wood   |
| 5   | Leaves |
| 6   | Sand   |
| 7   | Cobble |
| 8   | Planks |
| 9   | Gravel |

### Block Properties

- Vertex-colored
- Solid set for collision checks
- Face shading multiplier per direction

Lighting is baked per-face using brightness multipliers.

---

# 🧵 Meshing System

The engine uses **face culling meshing**:

For every block:

- Check 6 directions
- Only render face if adjacent block is non-solid

This results in:

- Massive triangle reduction
- Efficient geometry generation
- Fast rebuild performance

Each chunk:

- Generates a `THREE.BufferGeometry`
- Uses `MeshLambertMaterial`
- Vertex colors enabled

---

# 🧍 Player System

### Movement

- WASD → Movement
- Space → Jump
- F → Toggle Fly Mode
- Mouse → Look

### Physics

- Gravity: `22`
- Jump Velocity: `9`
- AABB Collision
- Horizontal & Vertical resolution separated

### Camera

- First-person
- YXZ rotation order
- Clamped pitch
- Pointer Lock API

---

# 🎯 Raycasting System

Custom step-based raycast:

- Max Reach: 5.5 blocks
- Step size: 0.05
- Detects first solid block
- Returns:
  - Target block
  - Previous empty block (for placement)

Used for:

- Breaking (LMB)
- Placing (RMB)

---

# 🎒 Inventory System

### Structure

```
Hotbar: 9 slots
Inventory: 4 rows
Total slots: 45
Stack size: 64
```

Stored as:

```
{ id: number, n: number }
```

Features:

- Auto stack merging
- Scroll hotbar
- Slot selection
- Visual block icons (canvas-rendered)

---

# 🔨 Crafting System (2×2)

Pattern-based deterministic crafting.

Recipes defined as:

```
{
  p: [id,id,id,id],
  out: blockID,
  n: amount
}
```

Matching:

```
r.p.every((id,i)=>slots[i].id===id)
```

Example Recipes:

| Input (2×2) | Output   |
| ----------- | -------- |
| Wood x4     | 4 Planks |
| Sand x4     | 4 Gravel |
| Cobble x4   | 4 Stone  |
| Planks x4   | 2 Wood   |

---

# 🗺️ Chunk Manager

### Responsibilities

- Generate chunks in render radius
- Mesh dirty chunks
- Remove far chunks
- Dispose geometries safely
- Memory cleanup

Two maps maintained:

```
cmap     → chunk data
meshMap  → rendered meshes
dirty    → chunks needing rebuild
```

---

# 💾 Save / Load System

Stored in `localStorage` under:

```
minrecraft_v1
```

Saved Data:

```
{
  version,
  player position,
  yaw/pitch,
  inventory,
  all generated chunks
}
```

Fully restores world state.

---

# 🖥 UI Systems

Includes:

- Crosshair
- Debug panel
- Hotbar
- Inventory UI
- Crafting UI
- Overlay (New / Load)
- Loading screen with progress bar
- Toast notifications

No external UI libraries used.

---

# 🔄 Game Loop

```
requestAnimationFrame
```

Per-frame tasks:

- Process input
- Apply physics
- Resolve collisions
- Update chunks
- Raycast
- Update debug
- Render scene

FPS calculated every 0.5s.

---

# 🎮 Controls

| Key    | Action          |
| ------ | --------------- |
| WASD   | Move            |
| Space  | Jump            |
| F      | Fly Mode        |
| LMB    | Break           |
| RMB    | Place           |
| 1-9    | Select Hotbar   |
| Scroll | Cycle Hotbar    |
| E      | Inventory       |
| Ctrl+S | Save            |
| Esc    | Close Inventory |

---

# ⚙️ Performance Characteristics

- Face culling meshing
- Chunk-based streaming
- Geometry disposal on unload
- Limited render distance
- No texture sampling (vertex color only)
- Minimal material count (1 per chunk)

Optimized for:

- Desktop browsers
- WebGL 1
- Mid-range GPUs

---

# 🧩 Design Philosophy

This engine prioritizes:

- Clarity over abstraction
- Data-oriented design
- Deterministic systems
- Zero external dependencies (besides Three.js)
- Full control of voxel pipeline

---

# 🚀 How To Run

1. Save file as `index.html`
2. Open in browser
3. Click **New World**
4. Pointer locks automatically

No server required.

---

# 🔮 Potential Extensions

- Greedy meshing
- Biomes
- Water blocks
- Day/Night cycle
- Lighting propagation
- Mobs
- Tools with durability
- Multiplayer via WebRTC
- Texture atlas instead of vertex colors
- Infinite vertical world
- World seeds

---

# 🧱 Technical Summary

| Feature            | Implemented |
| ------------------ | ----------- |
| Procedural Terrain | ✅          |
| Infinite Chunks    | ✅          |
| Collision          | ✅          |
| Gravity            | ✅          |
| Crafting           | ✅          |
| Inventory          | ✅          |
| Save / Load        | ✅          |
| Fly Mode           | ✅          |
| Raycasting         | ✅          |
| Chunk Streaming    | ✅          |

---

# 📜 License

Free to use, modify, extend.

---

## 👤 Author

®TSCREATES

GitHub: https://github.com/digitalguidance0-star
# VOXEL_ENGINE
