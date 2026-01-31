# Feature List

## MVP Features (Must Have)

### Core Mechanics
- [ ] 3D flight controller (player can fly around)
- [ ] Block placement on grid
- [ ] Block removal
- [ ] Base entity ("B") to protect
- [ ] Win/lose condition (base destruction)

### Grid System
- [ ] Dual grid implementation (Stalberg-style)
- [ ] Corner-based type definition
- [ ] 6 distinct tile models that cover all cases
- [ ] Grid barely visible in air

### Mesh Deformation
- [ ] Vertex handle system on model bounds
- [ ] Percentage-based vertex transformation
- [ ] Squash and stretch when grid moves
- [ ] Pieces remain connected during deformation

### Organic Grid Generation
- [ ] Quadrilateral-based grid (not triangles)
- [ ] Relaxation algorithm for even spacing
- [ ] Organic, non-repetitive look

### Performance (20,000+ FPS target)
- [ ] Hidden face culling
- [ ] Greedy meshing (2D)
- [ ] 32-bit vertex compression
- [ ] GPU instancing
- [ ] Triangle strips
- [ ] Indirect rendering (glMultiDrawElementsIndirect)
- [ ] SSBOs for chunk positions
- [ ] Direction-based chunk mesh splitting

### Game Modes
- [ ] Local play vs AI
- [ ] Split-time testing mode (play against yourself)

## Phase 2 Features (Nice to Have)
- [ ] Weapon crafting system
- [ ] Multiple block types
- [ ] Pattern matching for special pieces
- [ ] AI difficulty levels
- [ ] Sound effects

## Future Vision
- [ ] Multiplayer (network)
- [ ] Level editor
- [ ] Custom block skins
- [ ] Leaderboards
