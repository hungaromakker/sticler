# Feature List

## MVP Features - Sandbox Mode

### Hex-Sphere World
- [ ] Geodesic dome tessellation (icosahedron subdivision)
- [ ] Hexagonal tiles covering sphere surface
- [ ] 12 pentagon tiles at icosahedron vertices
- [ ] Cube coordinate system for hex math
- [ ] Neighbor relationship calculation (6 neighbors, 5 for pentagons)
- [ ] Beautiful stylized rendering (Hogwarts Legacy aesthetic)

### Camera & Controls
- [ ] 3D flight controller within hex territory
- [ ] Smooth camera movement
- [ ] Zoom in/out for detail vs overview
- [ ] Camera bounds to hex area

### Building System (Stalberg-Style)
- [ ] Dual grid implementation
- [ ] Corner-based type definition
- [ ] 6 distinct tile models covering all cases
- [ ] Block placement on barely-visible grid
- [ ] Block removal
- [ ] Mesh deformation (squash & stretch)
- [ ] Pieces remain connected during deformation

### Organic Grid Generation
- [ ] Quadrilateral-based grid (not triangles)
- [ ] Relaxation algorithm for even spacing
- [ ] Organic, non-repetitive look
- [ ] Vertex handle system on model bounds

### Economy System
- [ ] Pixel-based resource units
- [ ] Resource display UI
- [ ] Building costs pixels
- [ ] Resource cap per player
- [ ] (Sandbox: unlimited resources toggle)

### Performance (20,000+ FPS target)
- [ ] Hidden face culling
- [ ] Greedy meshing (2D)
- [ ] 32-bit vertex compression
- [ ] GPU instancing
- [ ] Triangle strips
- [ ] Indirect rendering (glMultiDrawElementsIndirect equivalent in wgpu)
- [ ] SSBOs for chunk positions
- [ ] Direction-based chunk mesh splitting

## Phase 2 Features - Local vs AI

### Territory Control
- [ ] Multiple hexes on small globe
- [ ] Hex ownership visualization (colors)
- [ ] Territory border rendering
- [ ] Conquest mechanics

### Combat
- [ ] Attack neighboring hex
- [ ] Defense strength based on structures
- [ ] Combat resolution
- [ ] Win/lose territory

### AI Opponent
- [ ] Simple AI that builds defenses
- [ ] AI attacks player hexes
- [ ] Difficulty levels

### Resource Generation
- [ ] Hexes generate resources over time
- [ ] More hexes = more income
- [ ] Conquest rewards

## Phase 3 Features - Small Multiplayer

### Local/LAN Play
- [ ] 2-4 players on same globe
- [ ] Turn-based or real-time
- [ ] Split screen or networked
- [ ] Player colors/factions

### Expanded Economy
- [ ] Trade between players?
- [ ] Resource types?
- [ ] Tech tree?

## Future Vision - Mass Multiplayer

### Persistent World
- [ ] Large globe with many hexes
- [ ] Many concurrent players
- [ ] Server infrastructure
- [ ] Account system

### Social Features
- [ ] Alliances
- [ ] Chat
- [ ] Leaderboards
- [ ] Spectator mode

### Advanced Gameplay
- [ ] Multiple resource types
- [ ] Tech trees
- [ ] Special abilities
- [ ] Events and seasons
