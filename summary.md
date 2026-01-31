# Project Summary for PRD Generation

**Project:** sticler

## What We're Building
A 3D hex-sphere conquest game where players control territories on a globe made of hexagonal tiles. Fight neighbors to expand, gather pixel-based resources, and build organic Stalberg-style castles and defenses. Visual style inspired by Hogwarts Legacy - stylized, magical, warm. Built with Bevy/Rust for 20,000+ FPS performance.

## Must Have
- Hex-sphere world (geodesic dome of hexagonal tiles)
- Territory control and conquest mechanics
- Pixel-based economy (resource caps, costs, generation)
- 3D flight controller within hex territories
- Block placement on organic dual-grid (Stalberg-style)
- Mesh deformation for seamless block blending
- 20,000+ FPS rendering (greedy meshing, GPU instancing, indirect rendering, SSBOs)
- Sandbox mode for building experimentation

## Must NOT Have (MVP)
- Networking/multiplayer (future phase)
- Realistic graphics (stylized only)
- Complex AI (simple opponent first)
- Unity/Unreal engine
- CPU-bound rendering
- Triangle-based grids

## Technical Stack
- **Engine:** Bevy (Rust) - maximum performance
- **Graphics API:** wgpu (Vulkan/Metal/DX12)
- **Hex Math:** Cube coordinates
- **Platform:** Linux primary, Windows secondary
- **Assets:** Premade stylized assets (Hogwarts Legacy aesthetic)

## Game Modes Roadmap
1. **MVP:** Sandbox - single hex, free building
2. **Phase 2:** Local vs AI - conquest on small globe
3. **Phase 3:** Small multiplayer - 2-4 players
4. **Future:** Mass multiplayer - persistent globe

## External Context Available
- [x] Reference image: hex-sphere globe (`.magicm/images/sticler/`)
- [x] Reference to `../stikcer_game/` for base patterns
- [ ] Hogwarts Legacy visual references (add to context/)
- [ ] Oscar Stalberg GDC talk notes (add to context/)
- [ ] Vercidium blog screenshots (add to context/)

## Success Looks Like
- Hex-sphere world renders beautifully
- Flying within hexes feels smooth
- Grid looks organic and magical (not blocky)
- Blocks blend seamlessly (dual-grid working)
- FPS counter shows 20,000+ consistently
- Fun to build in sandbox mode

---
**Ready for PRD generation. Run `/prd` to continue.**
