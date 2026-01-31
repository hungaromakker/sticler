# Original Prompt

We have the sticker game in the project directory but it feels bad on design. I want that idea implemented here but in 3D. You run around and build your weapons and defend your base and vice versa, and you bring toward your craft.

The project sticker focuses on the technical aspects of the game, not the fun part. The base concept is good there, but we want stylized 3D graphics, and 3D building your defense and 3D attacking the others' defense.

I don't mind using other engine for the base game. The base game should be local and against AI. Also with probably premade assets. So in test it's good to play against ourselves with split time.

## The World Vision

We need a beautiful stylized world with like a grid system in the air which is barely visible. Player can fly around and place their blocks like drawing it in with a given pixel count. They can work with bulky or not, and joining other building blocks like that's as they want as they like to protect the little B (base).

## Oscar Stalberg's Grid System

The grid should work like Oscar Stalberg's system (Townscaper, Bad North):

### 1. Dual Grid System
- Grid is offset by half a cell size
- Type (land/water) is defined for each corner of the tile, not the whole tile
- Reduces required 3D model variations from 15 to just 6 distinct tiles
- Both convex and concave corners remain perfectly rounded

### 2. Mesh Deformation (Squash and Stretch)
- Grid points are "shaken up" or randomized
- Uses points on model bounds as handles for deformation
- Every vertex transforms to a percentage of total width/height/depth
- When grid handles move, vertices move proportionally
- Pieces squash and stretch while remaining connected

### 3. Stalberg's Organic Quadrilateral Grid
- Creates organic-looking grid of quads (not triangles)
- Starts with hexagon-shaped points
- Connects into triangles, randomly dissolves edges to form quads
- Subdivides remaining triangles into three quads
- Divides every quad into four smaller quads
- Relaxation algorithm: each point becomes equally distant from neighbors

### 4. Special Pattern Matching
- Checks neighboring tiles for specific patterns
- Swaps individual tiles for special multi-tile pieces
- Adds artistic flair to procedural generation

## Performance Requirements (Vercidium Techniques)

Optimize for **20,000+ FPS** in full HD MVP:

### 1. Mesh Optimization
- Hidden face culling: skip rendering faces touching other voxels
- Greedy meshing: combine adjacent faces with same texture into single rectangle

### 2. Data Compression & Bit Masking
- Compress all vertex data (Position, Normals, Texture ID) into single 32-bit integer
- Use 6 bits per axis (X, Y, Z) for 32x32x32 chunks
- 3 bits for normals (6 possible cube faces)
- Bit masking (&) and shifting (>>) to unpack in vertex shader

### 3. Advanced OpenGL Rendering
- GPU Instancing: base model of single quad, instanced millions of times
- Triangle strips: reduce vertex count per face from 6 to 4
- Indirect Rendering (glMultiDrawElementsIndirect): one GPU command instead of thousands
- Shader Storage Buffer Objects (SSBO): store chunk world positions globally

### 4. Back-Face Culling at Chunk Level
- Split each chunk's mesh into six separate meshes (one per direction)
- Only send meshes facing toward player's camera to GPU
