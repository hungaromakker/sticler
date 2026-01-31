# Sticler - Refined Game Concept

## Core Concept
A 3D hex-sphere conquest game where players control territories on a globe made of hexagonal tiles. Start with one hex, fight neighbors to expand, gather resources, and build your castle bigger and bigger. Think Civilization meets Townscaper on a spherical world.

## World Structure

### The Hex-Sphere
- Planet/globe composed of hexagonal tiles (geodesic dome tessellation)
- Each hex is a controllable territory
- Players start controlling 1 hex
- 6 equidistant neighbors per hex (uniform connectivity)
- Beautiful stylized world like Hogwarts Legacy aesthetic

### Territory Control
- Fight neighboring hexes to conquer them
- Conquered hexes generate resources
- Build defenses within your controlled hexes
- Lose a hex = lose its resources and structures

## Building System

### Organic Grid (Stalberg-Style)
- Dual-grid system for seamless block placement
- Blocks blend organically (no sharp corners)
- Mesh deformation (squash & stretch) for natural look
- Barely-visible grid in the air within each hex

### Construction
- Fly around your hex territory in 3D
- Draw/place blocks to build defenses
- Blocks join and deform automatically
- Build castles, walls, towers, weapons

## Economy System

### Pixel-Based Resources
- Building costs "pixels" (resource units)
- Maximum resource cap per player
- Controlled hexes generate resources over time
- Conquering neighbors grants their resources

### Cost Balancing
- Small blocks = cheap
- Large structures = expensive
- Weapons = resource investment
- Defense vs offense tradeoff

## Game Modes (Vision)

### MVP - Sandbox Mode
- Single hex, unlimited resources
- Free building and experimentation
- Test mechanics and controls

### Phase 2 - Local vs AI
- Multiple hexes on small globe
- Simple AI opponent
- Conquest mechanics

### Phase 3 - Small Multiplayer
- 2-4 players on same globe
- Local or LAN play

### Future - Mass Multiplayer
- Many players on large globe
- Persistent world
- Alliances and politics

## Visual Style
- Harry Potter Hogwarts Legacy aesthetic
- Stylized 3D, warm and magical
- Ethereal, beautiful world
- Not realistic - artistic and dreamy

## What Makes It Different from sticker_game
| sticker_game | sticler |
|--------------|---------|
| Flat 2D world | Hex-sphere globe |
| Single arena | Territory conquest |
| No economy | Pixel-based resources |
| Technical focus | Fun-focused gameplay |
| Basic grid | Stalberg organic dual-grid |
| Standard rendering | Hyper-optimized voxel rendering |

## Technical Foundation
- **Engine:** Bevy (Rust) for maximum performance
- **Rendering:** Vercidium optimizations (greedy meshing, GPU instancing, indirect rendering, SSBOs)
- **Target:** 20,000+ FPS capability
- **Hex math:** Cube coordinates for hex operations
