# Project Summary for PRD Generation

**Project:** sticler

## What We're Building
A 3D stylized tower defense/attack game where players fly through an ethereal world with an organic grid system (Oscar Stalberg-style), build defenses with seamlessly-blending blocks, and attack opponent bases. Hyper-optimized for 20,000+ FPS using Vercidium's voxel rendering techniques.

## Must Have
- 3D flight controller
- Block placement/removal on organic dual-grid
- Stalberg-style mesh deformation (squash & stretch)
- Quadrilateral-only grid with relaxation algorithm
- 20,000+ FPS rendering (greedy meshing, GPU instancing, indirect rendering, SSBOs)
- Base protection/destruction win condition
- Local play vs AI
- Split-time testing mode

## Must NOT Have
- Realistic graphics (stylized only)
- Multiplayer/networking
- Complex crafting systems
- Triangle-based grids
- CPU-bound rendering
- Unity/Unreal engine

## Technical Stack
- **Engine:** Bevy (Rust) or Godot 4 with custom rendering
- **Graphics API:** OpenGL 4.5+ or Vulkan
- **Platform:** Linux primary, Windows secondary
- **Assets:** Premade stylized/voxel assets

## External Context Available
- [x] Reference to `../stikcer_game/` for base patterns
- [ ] Oscar Stalberg GDC talk notes (add to context/)
- [ ] Vercidium blog screenshots (add to context/)
- [ ] Art style references (add to context/)

## Success Looks Like
- Flying feels smooth and responsive
- Grid looks organic and beautiful (not blocky)
- Blocks blend seamlessly (dual-grid working)
- FPS counter shows 20,000+ consistently
- Fun to play even in basic form

---
**Ready for PRD generation. Run `/prd` to continue.**
