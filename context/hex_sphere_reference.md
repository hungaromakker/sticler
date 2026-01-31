# Hex-Sphere World Reference

## Visual Reference
- **Image:** `.magicm/images/sticler/20260131_205420_096d29dd.jpg`
- **Description:** 3D rendered globe/sphere made of hexagonal tiles in orange and blue colors
- **Style:** Clean, geometric, colorful territories on a spherical surface

## Technical Details (from Grokipedia)

### Hexagonal Grid Properties
- Six equidistant adjacent cells per hex
- Uniform connectivity simplifies pathfinding
- Smoother representation of organic terrain vs square grids
- No diagonal distortion issues

### Coordinate System
- Cube coordinates recommended for hex math
- Subtle numbering along edges for tracking
- Each hex represents fixed area/distance

### Game Design Applications
- Strategy games (Civilization series uses hex)
- Territory control mechanics
- Resource distribution
- Turn-based or real-time conquest

## Implementation Notes

### Geodesic Dome Tessellation
To create a hex-sphere:
1. Start with icosahedron (20 triangles)
2. Subdivide triangles
3. Project vertices onto sphere
4. Convert to hexagonal dual (pentagons at 12 icosahedron vertices)

### Neighbor Relationships
- Most hexes have 6 neighbors
- 12 special pentagons have 5 neighbors (icosahedron vertices)
- Use cube coordinates for easy neighbor calculation

## Visual Style Target
- **Reference:** Harry Potter Hogwarts Legacy
- **Feel:** Magical, warm, stylized
- **Not:** Realistic, cold, geometric
- **Colors:** Rich, warm palette with ethereal glow
