# Technical Constraints

## Performance Requirements
- **Target FPS:** 20,000+ in full HD (1920x1080)
- **Minimum FPS:** Never drop below 60 FPS on any hardware
- **GPU Memory:** Efficient chunk management

## Rendering Pipeline (Required Techniques)

### Voxel Optimization Stack
1. **Hidden Face Culling** - Skip faces touching other voxels
2. **Greedy Meshing** - Combine adjacent same-texture faces
3. **32-bit Vertex Compression** - Pack position, normals, texture ID
4. **GPU Instancing** - Single quad model, instanced millions of times
5. **Triangle Strips** - 4 vertices per face instead of 6
6. **Indirect Rendering** - glMultiDrawElementsIndirect
7. **SSBOs** - Store chunk positions in Shader Storage Buffer Objects
8. **Direction-Split Meshes** - 6 meshes per chunk for backface culling

### Grid System Requirements
- Dual grid (Stalberg-style)
- Quadrilateral-only cells
- Mesh deformation with vertex handles
- Relaxation algorithm for organic look

## Engine Options
- **Bevy (Rust)** - Preferred for performance
- **Godot 4** - Acceptable with custom rendering
- **Custom engine** - If neither works
- **NOT Unity/Unreal** - Too heavy for our optimization needs

## Platform
- **Primary:** Linux (development)
- **Secondary:** Windows
- **API:** OpenGL 4.5+ or Vulkan

## Asset Requirements
- Premade assets acceptable for MVP
- Stylized aesthetic (low-poly or voxel)
- 6 basic tile models for dual-grid system
