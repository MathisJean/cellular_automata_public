# CA Simulator

**A 2D Metroidvania built around a Noita-inspired falling-sand cellular automaton engine.** C++, raylib, built from scratch, solo.

![C++](https://img.shields.io/badge/C++-00599C?style=flat-square&logo=c%2B%2B&logoColor=white)
![raylib](https://img.shields.io/badge/raylib-000000?style=flat-square&logo=c&logoColor=white)
![Status](https://img.shields.io/badge/status-active%20development-brightgreen?style=flat-square)
![Private](https://img.shields.io/badge/repo-private-lightgrey?style=flat-square)

> [!NOTE]
> Long-term goal is a large-world simulation with deep physics fidelity — this project prioritizes quality foundations over expedient shortcuts throughout.

---

## What it is

A falling-sand style cellular automaton (think *Noita*) as the physics backbone for a 2D Metroidvania. Every grain of sand, drop of liquid, or reactive element is simulated, not faked — which means the rendering and simulation layers have to be built with real separation of concerns from day one.

## Architecture

**Rendering** — a GLSL shader pipeline: `id_texture`, `var_texture`, `temp_texture`, palette textures, a 256×1 params texture, a persistent `color_rt` render target, and a ping-pong post-process pass stack.

**World** — chunk-based, with dirty-rect tracking and a chunk sleep/wake system so idle regions of the world don't cost simulation time.

**Camera** — a wrapper around raylib's `Camera2D` supporting multi-target weighted-centroid blending across various modes

**Elements** self-register via static initializers in their own `.cpp` files — no central registry to maintain.

Reference material: Petri Purho's Noita GDC talk, The Powder Toy, the raylib source, and youtube videos.

[Optimizing a Falling Sand Simulation To an Unreasonable Degree](https://www.youtube.com/watch?v=HrrJxkRlRfk) — NivMiz  
[How To Code a Falling Sand Simulation (like Noita) with Cellular Automata](https://www.youtube.com/watch?v=5Ka3tbbT-9E) — MARF  
[Recreating Noita's Sand Simulation in C and OpenGL | Game Engineering](https://www.youtube.com/watch?v=VLZjd_Y1gJ8) — John Jackson  
[How I made a simple Falling Sand Simulation in C++](https://www.youtube.com/watch?v=iPOxKzNmRS0) — Kung

<details>
<summary><strong>Implementation</strong></summary>

**Architecture**
- World buffers use absolute world coordinates, not view-relative — decouples rendering from camera position cleanly
- Corner rounding using line trajectory through corners and calculating overlap % 
- Chunk sleep state still has to walk dirty flags every frame, or stale `id_buffer` bytes leave gray trails on moved cells

**Rendering**
- `gl_FragCoord`-based world UV reconstruction (`floor(gl_FragCoord.xy)` directly) avoids an unnecessary divide-then-multiply round trip
- `rlActiveTextureSlot` is required when raylib's automatic `texture0` binding conflicts with named uniforms
- `rlDisableColorBlend` is needed during world shader patches to prevent alpha ghost trails
- A persistent `color_rt` that patches only dirty regions dramatically improves performance on settled worlds

</details>

## Current state

The core engine is substantially built. Active work is on rendering correctness and camera-to-world coordinate mapping — currently chasing a placement offset bug.

## Roadmap

1. Full simulation physics — thermal systems, complete element roster with reactions
2. Lighting — emissive palettes, multi-pass propagation
3. Camera unlocks — viewport culling, sprite batching, correct UV math downstream
4. Post-process effects — heat shimmer, bloom
5. Player-world interaction refinements

---

*Solo Developer — Apr 2026 to present*
