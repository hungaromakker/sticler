# PRD: Sticler - Phase 1 Playable Prototype

## Introduction

Sticler is a 3D stylized tower defense/conquest game where players fly around a **hex-sphere globe**, build organic-looking defenses using an Oscar Stalberg-style dual-grid system, and battle a **strategic AI opponent**. This Phase 1 PRD delivers a **visually stunning playable prototype** with: full hex-sphere world, Stalberg's organic mesh deformation, Vercidium's hyper-optimized voxel rendering (20,000+ FPS), **6 block types** for strategic depth, **beautiful Hogwarts Legacy-style art assets**, timer-based rounds, attack units with pathfinding, and a **complex AI** that adapts to player strategy.

**Tech Stack:** Bevy (Rust) + wgpu
**Visual Style:** Hearthstone-inspired warm, welcoming aesthetic (cozy, painterly, NO harsh lines)

## Goals

### Primary Goal: Beautiful Playable Prototype on Hex-Sphere World
Deliver a visually stunning, gameplay-complete prototype on a full hex-sphere globe.

**Achieved when:**
- Hex-sphere globe world with territories player can conquer
- Player can fly around and place blocks that blend organically (Stalberg style)
- Beautiful Hearthstone-style warm, welcoming 3D assets (NO harsh lines)
- Multiple block types with distinct visual styles
- Complex AI that strategizes, not just random placement
- Build phase → Attack phase timer creates tension
- Attack units path toward enemy core and destroy blocks
- Win/lose condition feels satisfying
- Sample scene demonstrates 20,000+ FPS rendering capability

### Secondary Goals:
- Full Stalberg dual-grid + mesh deformation working from day one (organic look, not blocky)
- Vercidium rendering pipeline ready for massive worlds
- Architecture supports future multiplayer expansion

## User Stories

---

### FOUNDATION PHASE (No Dependencies - Run in Parallel)

---

### US-001: Initialize Bevy project with wgpu rendering
**Dependencies:** None
**Description:** As a developer, I need a Bevy project structure with wgpu rendering foundation for high-performance 3D graphics.

**Why This Matters:**
This is the foundation everything builds on. Bevy + wgpu gives us Vulkan/Metal/DX12 access for the Vercidium-style optimizations we need later. Getting this right means we won't need to rewrite rendering later.

**Goal:** Working Bevy app that opens a window with a 3D camera and basic scene.

**Acceptance Criteria:**
- [ ] Create `Cargo.toml` with bevy 0.15+ and required dependencies
- [ ] Create `src/main.rs` with Bevy app initialization
- [ ] Window opens with configurable resolution (default 1920x1080)
- [ ] 3D perspective camera with fly-style controls (WASD + mouse look)
- [ ] Basic lighting setup (directional light for sun, ambient)
- [ ] FPS counter displayed on screen
- [ ] `cargo build --release` compiles without errors
- [ ] Typecheck passes (`cargo check`)

**Success looks like:** Launch the game, fly around an empty 3D space with smooth camera controls, see FPS counter showing high frame rate.

---

### US-002: Create hex territory data structures
**Dependencies:** None
**Description:** As a developer, I need data structures representing a single hexagonal territory with cube coordinates.

**Why This Matters:**
Hex coordinates are the foundation of the entire game world. Using cube coordinates (x, y, z where x+y+z=0) makes hex math elegant and enables future hex-sphere expansion. This isn't just a struct - it's the spatial foundation of the game.

**Technical Detail:**
Cube coordinates for hexes: any hex is represented as (x, y, z) where x + y + z = 0. This constraint makes distance calculations, rotations, and neighbor-finding trivial.

**Goal:** Hex coordinate system that supports future hex-sphere world.

**Acceptance Criteria:**
- [ ] Create `src/hex/mod.rs` module
- [ ] `HexCoord` struct with `x: i32, y: i32, z: i32` (cube coordinates)
- [ ] Implement constraint validation: `x + y + z == 0`
- [ ] Implement `distance(a, b)` function between two hexes
- [ ] Implement `neighbors()` returning 6 adjacent hex coordinates
- [ ] Implement `PartialEq, Eq, Hash` for HashMap key usage
- [ ] Unit tests for coordinate math
- [ ] Typecheck passes

**Success looks like:** Create hex coordinates, calculate distances, find neighbors - all hex math works correctly.

---

### US-003: Create dual-grid data structures (Stalberg system)
**Dependencies:** None
**Description:** As a developer, I need the dual-grid data structure that enables organic block blending.

**The Stalberg Innovation:**
Oscar Stalberg's genius was using a DUAL grid - offset by half a cell. Instead of storing block type per cell, we store block type per CORNER. This means:
- Convex corners blend smoothly (easy)
- Concave corners ALSO blend smoothly (hard problem solved!)
- Only 6 tile types needed instead of 15

**How It Works:**
```
Primary Grid:     Dual Grid (offset 0.5):
+---+---+         ·   ·   ·
|   |   |           +---+
+---+---+         ·   ·   ·
|   |   |           +---+
+---+---+         ·   ·   ·
```
Corners of primary cells = centers of dual cells.

**Goal:** Data structures enabling Stalberg-style organic mesh blending.

**Acceptance Criteria:**
- [ ] Create `src/grid/mod.rs` module
- [ ] `GridVertex` struct: position in 3D space, corner type enum
- [ ] `DualCell` struct: references 4 corner vertices
- [ ] `GridLayer` struct: one Z-level of the building grid
- [ ] `BuildingGrid` struct: stack of GridLayers (3D grid)
- [ ] Method to get corner states for any dual cell
- [ ] Unit tests for dual-grid relationships
- [ ] Typecheck passes

**Success looks like:** Create a grid, query corner states, dual-cell relationships work correctly.

---

### US-004: Create voxel mesh data structures (Vercidium foundation)
**Dependencies:** None
**Description:** As a developer, I need mesh data structures optimized for GPU instancing and indirect rendering.

**The Vercidium Approach:**
To hit 20,000+ FPS, we need:
1. **Greedy meshing** - combine adjacent same-type blocks into larger quads
2. **GPU instancing** - one draw call for thousands of blocks
3. **Indirect rendering** - GPU decides what to draw, not CPU
4. **SSBOs** - Shader Storage Buffer Objects for massive data

This story creates the DATA STRUCTURES. Later stories implement the algorithms.

**Goal:** GPU-friendly mesh structures ready for hyper-optimization.

**Acceptance Criteria:**
- [ ] Create `src/render/mesh.rs` module
- [ ] `VoxelFace` struct: position, normal, size (for greedy meshing)
- [ ] `InstanceData` struct: per-instance transform + material for GPU
- [ ] `MeshChunk` struct: collection of faces in a spatial region
- [ ] `ChunkManager` struct: manages all chunks, tracks dirty state
- [ ] Structures use `#[repr(C)]` for GPU compatibility
- [ ] Typecheck passes

**Success looks like:** Data structures compile, are GPU-layout compatible, ready for rendering implementation.

---

### US-005: Create pixel economy data structures
**Dependencies:** None
**Description:** As a developer, I need data structures for the pixel-based resource economy.

**Economy Design:**
Players have "pixels" as currency:
- Building blocks costs pixels
- Spawning attack units costs pixels
- Controlled territory generates pixels
- There's a cap (starts at 100, upgradeable)

This creates the core strategic tension: spend on defense or offense?

**Goal:** Economy system that tracks resources and enables strategic choices.

**Acceptance Criteria:**
- [ ] Create `src/economy/mod.rs` module
- [ ] `PlayerEconomy` struct: current pixels, max cap, income rate
- [ ] `BuildCost` enum: costs for each block/unit type
- [ ] Method: `can_afford(cost) -> bool`
- [ ] Method: `spend(cost) -> Result<(), NotEnoughPixels>`
- [ ] Method: `add_income(amount)` respecting cap
- [ ] Constants for initial values (100 cap, 20 income, costs from gameplay doc)
- [ ] Unit tests for economy math
- [ ] Typecheck passes

**Success looks like:** Economy tracks pixels, spending works, cap is respected.

---

### US-006: Create game state machine
**Dependencies:** None
**Description:** As a developer, I need a state machine for round phases (Build → Attack → Resolution).

**Phase Flow:**
```
BUILD (30-60s) → ATTACK (60-90s) → RESOLUTION → BUILD...
```

Each phase has different rules:
- BUILD: Can place blocks, can't attack
- ATTACK: Can send units, can't build
- RESOLUTION: Attacks resolve, damage applied

**Goal:** Clear phase separation enabling phase-specific gameplay rules.

**Acceptance Criteria:**
- [ ] Create `src/game/state.rs` module
- [ ] `GamePhase` enum: `Build`, `Attack`, `Resolution`
- [ ] `PhaseTimer` struct: current phase, time remaining
- [ ] `GameState` resource: phase, round number, player states
- [ ] Bevy state transitions for phase changes
- [ ] Events: `PhaseStarted(GamePhase)`, `PhaseEnded(GamePhase)`
- [ ] Configurable phase durations (30s build, 60s attack default)
- [ ] Typecheck passes

**Success looks like:** Game transitions through phases automatically, events fire correctly.

---

### US-007: Create attack unit data structures
**Dependencies:** None
**Description:** As a developer, I need data structures for attack units that path toward enemy core.

**Unit Design:**
- Units are spawned during Attack phase
- They path toward enemy's core (center of hex)
- They damage blocks in their way
- Different unit types: Basic (cheap, 1 damage), Heavy (expensive, 3 damage)

**Goal:** Unit data structures ready for spawning and pathfinding.

**Acceptance Criteria:**
- [ ] Create `src/combat/units.rs` module
- [ ] `UnitType` enum: `Basic`, `Heavy`
- [ ] `AttackUnit` struct: position, type, health, damage, target
- [ ] `UnitStats` for each type (speed, health, damage, cost)
- [ ] Component for Bevy ECS integration
- [ ] Typecheck passes

**Success looks like:** Unit types defined with stats, ready for spawning system.

---

### US-008: Implement hex-sphere geodesic tessellation
**Dependencies:** US-002
**Description:** As a developer, I need to generate a hex-sphere (geodesic dome) world where each hex is a controllable territory.

**The Geodesic Approach:**
A hex-sphere is created by:
1. Start with an icosahedron (20 triangles)
2. Subdivide each triangle recursively
3. Project vertices onto sphere surface
4. Convert triangular faces to hex grid (with 12 pentagons)

**Why Hex-Sphere:**
- Globe provides natural conquest territory
- All hexes are neighbors (6 each, pentagons have 5)
- Beautiful organic world feel
- Matches the vision from the 11.jpg reference

**Goal:** Spherical world with ~100 hex territories.

**Acceptance Criteria:**
- [ ] Create `src/world/hex_sphere.rs` module
- [ ] Generate icosahedral geodesic sphere (subdivision level configurable)
- [ ] Convert triangular mesh to hex grid
- [ ] Handle the 12 pentagon exceptions correctly
- [ ] Each hex has 3D position, normal, and neighbors list
- [ ] Configurable world size (50-500 hexes)
- [ ] Unit tests for neighbor connectivity
- [ ] Typecheck passes

**Success looks like:** Generate a sphere of hexes, query any hex for its neighbors, all hexes properly connected.

---

### US-009: Render hex-sphere world with warm, cozy aesthetic
**Dependencies:** US-008, US-001
**Description:** As a player, I want to see a beautiful world that feels welcoming - like a storybook globe.

**Visual Design - HEARTHSTONE/STORYBOOK FEEL:**
- Warm, painted-style globe - like a hand-crafted world
- **NO harsh hex lines** - territories blend naturally like terrain
- Owned territories have warmer, more vibrant colors
- Unowned territories are softer, muted (but still friendly)
- Atmospheric glow: golden hour warmth, sunset sky
- Background: dreamlike clouds, soft sunset - NOT cold space

**Terrain-Style Ownership:**
Instead of hex borders, territories look like:
- Your territory: lush golden-green meadow feel
- Enemy territory: slightly cooler blue-green meadow
- Neutral: soft earth tones, peaceful
- All blend into each other organically at edges

**Goal:** World feels like a cozy storybook you want to explore.

**Acceptance Criteria:**
- [ ] Create `src/world/render.rs` module
- [ ] Render hex-sphere as 3D mesh with painterly textures
- [ ] NO visible hex border lines - territories blend naturally
- [ ] Territory colors based on ownership (warm gradients)
- [ ] Atmospheric shader: golden hour glow, soft warmth
- [ ] Skybox: sunset clouds, dreamy (NOT dark space)
- [ ] Camera can orbit and zoom with smooth easing
- [ ] World feels like a tabletop game piece, friendly
- [ ] Overall impression: "cozy", "inviting", "I want to play there"
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** See the world, feel welcomed, want to start building immediately.

---

### US-010: Foundation Phase Validation Checkpoint
**Dependencies:** US-001, US-002, US-003, US-004, US-005, US-006, US-007, US-008, US-009
**Description:** Validate all Phase 1 foundation files for type safety, naming coherence, and integration readiness.

**Why This Matters:**
Before building on the foundation, we verify everything compiles together, modules don't have circular dependencies, and naming is consistent.

**Acceptance Criteria:**
- [ ] `cargo check` passes with no errors
- [ ] `cargo clippy` passes with no warnings (or justified allows)
- [ ] All modules properly export their public types
- [ ] No circular dependencies between modules
- [ ] Consistent naming: snake_case files, PascalCase types
- [ ] All structs have Debug derive for debugging
- [ ] Unit tests pass: `cargo test`
- [ ] Typecheck passes

**Success looks like:** `cargo check && cargo clippy && cargo test` all pass cleanly.

---

### RENDERING PHASE (Depends on Foundation)

---

### US-011: Implement greedy meshing algorithm
**Dependencies:** US-004, US-003
**Description:** As a developer, I need greedy meshing to combine adjacent blocks into larger quads for fewer draw calls.

**The Algorithm:**
Greedy meshing scans through voxels and combines adjacent same-type faces:
```
Before: 16 individual block faces (16 quads)
After:  1 large quad covering all 16 blocks
```

This reduces vertex count by 10-100x in typical builds.

**Implementation:**
1. For each face direction (6 directions)
2. Scan 2D slice of blocks
3. Find largest rectangle of same-type
4. Emit one quad, mark as processed
5. Continue until all processed

**Goal:** Dramatic reduction in mesh complexity for performance.

**Acceptance Criteria:**
- [ ] Create `src/render/greedy_mesh.rs`
- [ ] `greedy_mesh(chunk: &MeshChunk) -> Vec<VoxelFace>` function
- [ ] Handles all 6 face directions
- [ ] Correctly merges adjacent same-type blocks
- [ ] Preserves material/color information
- [ ] Unit test: 4x4 same blocks → 1 quad per face
- [ ] Unit test: checkerboard pattern → no merging
- [ ] Benchmark showing reduction ratio
- [ ] Typecheck passes

**Success looks like:** 1000 adjacent blocks become ~6 quads instead of 6000.

---

### US-012: Implement GPU instancing for blocks
**Dependencies:** US-004, US-001
**Description:** As a developer, I need GPU instancing to render thousands of blocks with a single draw call.

**Why Instancing:**
Without instancing: 1000 blocks = 1000 draw calls = slow
With instancing: 1000 blocks = 1 draw call = fast

We upload instance data (transforms, colors) to a buffer, then draw once with instance count.

**Goal:** Single draw call renders all blocks of same type.

**Acceptance Criteria:**
- [ ] Create `src/render/instancing.rs`
- [ ] Create instance buffer with transforms + colors
- [ ] Custom shader that reads instance data
- [ ] Single draw call renders all instances
- [ ] Support for at least 100,000 instances
- [ ] Hot-reload instance buffer when blocks change
- [ ] FPS counter shows improvement vs naive rendering
- [ ] Typecheck passes

**Success looks like:** Add 10,000 blocks, FPS stays high, only a few draw calls.

---

### US-013: Implement indirect rendering
**Dependencies:** US-012
**Description:** As a developer, I need indirect rendering where GPU decides what to draw.

**Why Indirect:**
Normal: CPU tells GPU "draw 1000 instances"
Indirect: GPU buffer contains draw commands, GPU reads them directly

This eliminates CPU-GPU sync for draw counts. Essential for dynamic worlds.

**Goal:** GPU-driven draw calls for maximum performance.

**Acceptance Criteria:**
- [ ] Create `src/render/indirect.rs`
- [ ] Indirect draw command buffer on GPU
- [ ] Compute shader or CPU can write draw commands
- [ ] GPU reads commands and executes draws
- [ ] No CPU readback needed for draw counts
- [ ] Works with existing instancing system
- [ ] Typecheck passes

**Success looks like:** Modify visible blocks, GPU automatically adjusts draw count without CPU sync.

---

### US-014: Implement SSBO-based mesh storage
**Dependencies:** US-012
**Description:** As a developer, I need Shader Storage Buffer Objects for massive mesh data.

**Why SSBOs:**
Uniform buffers are limited (~64KB). SSBOs can hold gigabytes.
For 20,000+ FPS with massive worlds, we need SSBO-based vertex data.

**Goal:** Mesh data stored in SSBOs for unlimited scale.

**Acceptance Criteria:**
- [ ] Create `src/render/ssbo.rs`
- [ ] Vertex data stored in SSBO (not vertex buffer)
- [ ] Shader reads vertices from SSBO using gl_VertexIndex
- [ ] Support for dynamic resizing
- [ ] Memory management (allocate, free, defragment)
- [ ] Works with instancing and indirect systems
- [ ] Typecheck passes

**Success looks like:** Mesh data in SSBO, shader reads it correctly, no size limits.

---

### US-015: Create Stalberg mesh deformation system
**Dependencies:** US-003, US-011
**Description:** As a developer, I need mesh deformation so blocks blend organically like Townscaper.

**The Stalberg Magic:**
Blocks don't just snap together - they MORPH:
1. **Squash & Stretch:** Blocks deform to fill gaps
2. **Corner Rounding:** Sharp corners become smooth curves
3. **Organic Flow:** Structures look grown, not placed

**Implementation:**
- Query dual-grid corner states
- Select appropriate mesh variant (6 types)
- Apply vertex displacement based on neighbors
- Blend normals for smooth lighting

**Goal:** Blocks look organic and magical, not Minecraft-blocky.

**Acceptance Criteria:**
- [ ] Create `src/render/deformation.rs`
- [ ] 6 base mesh variants for corner configurations
- [ ] Vertex displacement based on neighbor states
- [ ] Smooth normal blending at edges
- [ ] Works with greedy meshing (deform merged quads)
- [ ] Visual test: place blocks, verify organic blending
- [ ] Typecheck passes

**Success looks like:** Place blocks anywhere, they morph together into organic shapes like Townscaper.

---

### US-016: Rendering Phase Validation Checkpoint
**Dependencies:** US-011, US-012, US-013, US-014, US-015
**Description:** Validate rendering pipeline integration and performance.

**Acceptance Criteria:**
- [ ] All rendering modules compile together
- [ ] Create test scene with 10,000+ blocks
- [ ] FPS counter shows 1000+ FPS on test scene (release build)
- [ ] Greedy meshing visibly reduces draw calls
- [ ] Instancing confirmed working (single draw per material)
- [ ] Stalberg deformation visually working (organic look)
- [ ] No rendering artifacts or z-fighting
- [ ] `cargo clippy` passes
- [ ] Typecheck passes

**Success looks like:** 10,000 blocks render at 1000+ FPS with organic appearance.

---

### GAMEPLAY PHASE (Depends on Rendering)

---

### US-017: Implement friendly block placement system
**Dependencies:** US-003, US-015, US-005
**Description:** As a player, I want to place blocks naturally - without visible grid lines.

**The Feel - COZY BUILDING:**
- **NO grid lines visible** - building feels organic, not technical
- Ghost block preview appears where you're pointing (soft, golden glow)
- Preview block has friendly outline (soft, not harsh)
- Click to place - satisfying "plop" with warm sparkles
- Blocks morph together like clay being shaped
- Building feels like crafting, not technical placement

**Visual Feedback:**
- Ghost preview: semi-transparent, warm golden glow
- Placement: golden dust particles, soft sound
- Block morphs: smooth animation to blend with neighbors
- Invalid position: gentle shake (not harsh red X)

**Goal:** Building feels like playing with friendly blocks, not programming a grid.

**Acceptance Criteria:**
- [ ] Create `src/building/placement.rs`
- [ ] Ray-cast from camera to find valid placement position
- [ ] Ghost block preview with warm, soft glow (not technical outline)
- [ ] **NO visible grid lines** - blocks just snap to valid positions
- [ ] Left click places block (if can afford)
- [ ] Right click removes block (gentle poof, partial refund)
- [ ] Block immediately runs Stalberg organic deformation
- [ ] Warm particle effect on placement (golden sparkles)
- [ ] Economy updated on place/remove
- [ ] Sound effect: friendly "plop" (think board game piece)
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** Fly around, point at a spot, see friendly ghost block, click to place - feels like playing with toys.

---

### US-018: Implement soft, organic hex territory boundaries
**Dependencies:** US-002, US-001
**Description:** As a player, I want to subtly see my territory - but NO harsh grid lines.

**Visual Design - SOFT & ORGANIC:**
- **NO visible grid lines** - the world should feel natural, not technical
- Territory boundary is organic: think gentle terrain color shift, soft light gradient
- Like natural landscape zones (grassy area fading to sandy area)
- Subtle particle effects at edges (drifting golden dust)
- Core glows warmly - you know your territory by proximity to core

**Boundary Options (pick best during implementation):**
1. **Terrain tint shift** - your area has warmer ground tones
2. **Ambient light pool** - soft light radiates from your core
3. **Floating particles** - gentle sparkles define your area
4. **Soft fog/mist** - light mist at territory edges

**What to AVOID:**
- Hard hexagonal lines
- Visible grid overlays
- Tron-style neon boundaries
- Technical-looking UI elements

**Goal:** Know where your territory is without seeing any lines.

**Acceptance Criteria:**
- [ ] Create `src/hex/boundary.rs`
- [ ] Territory ownership visible WITHOUT grid lines
- [ ] Use soft color gradient, light, or particles (not lines)
- [ ] Player core rendered at hex center with warm glow
- [ ] Core glow intensity indicates territory range
- [ ] Core has health bar (subtle, friendly design)
- [ ] Enemy territory has different ambient color (cooler tones)
- [ ] Flying near boundary edge, you sense transition subtly
- [ ] Screenshot test: should look like natural terrain, not tech grid
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** You know your territory, enemy territory - but no lines visible. Just feels like natural zones.

---

### US-019: Implement phase timer UI
**Dependencies:** US-006, US-001
**Description:** As a player, I want to see the phase timer so I know how long I have to build/attack.

**UI Design:**
- Large timer in top center
- Phase name displayed: "BUILD PHASE" / "ATTACK PHASE"
- Timer counts down with urgency colors (green → yellow → red)
- Sound cue at phase transitions

**Goal:** Clear time pressure that creates urgency.

**Acceptance Criteria:**
- [ ] Create `src/ui/timer.rs`
- [ ] Timer displayed prominently (top center)
- [ ] Shows current phase name
- [ ] Countdown with seconds
- [ ] Color changes: >15s green, 5-15s yellow, <5s red
- [ ] Pulsing effect when low time
- [ ] Phase transition sound (placeholder beep)
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** See "BUILD PHASE - 23s" counting down, urgency when time is low.

---

### US-020: Implement economy UI
**Dependencies:** US-005, US-001
**Description:** As a player, I want to see my pixel count so I can plan my spending.

**UI Design:**
- Pixel counter in corner
- Shows current / max (e.g., "45 / 100")
- Income indicator (+20/round)
- Flashes when insufficient funds

**Goal:** Clear resource visibility for strategic decisions.

**Acceptance Criteria:**
- [ ] Create `src/ui/economy.rs`
- [ ] Display current pixels / max cap
- [ ] Display income per round
- [ ] Flash red when trying to build without funds
- [ ] Animate pixel changes (count up/down)
- [ ] Block costs shown on hover/selection
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** Always know how many pixels you have, see income, understand costs.

---

### US-021: Implement multiple block types
**Dependencies:** US-017, US-005
**Description:** As a player, I want different block types so I can create varied defenses.

**Block Types:**

| Block | Cost | HP | Special |
|-------|------|-----|---------|
| **Basic Wall** | 5 px | 1 | Standard block |
| **Reinforced Wall** | 15 px | 3 | Tougher, slower to destroy |
| **Spike Trap** | 10 px | 1 | Damages units passing through |
| **Tower Base** | 20 px | 2 | Can build turrets on top |
| **Shield Block** | 25 px | 2 | Absorbs 1 hit for adjacent blocks |
| **Decoy Block** | 8 px | 1 | Attracts units, low HP |

**Goal:** Strategic variety in building options.

**Acceptance Criteria:**
- [ ] Create `src/building/block_types.rs` module
- [ ] Enum for all 6 block types with stats
- [ ] Each block type has distinct visual mesh
- [ ] Block selector UI (radial menu or hotbar)
- [ ] Different costs per block type
- [ ] Spike trap damages units (1 damage on contact)
- [ ] Tower base allows turret placement
- [ ] Shield block absorbs hits for neighbors
- [ ] Decoy attracts pathfinding units
- [ ] All blocks work with Stalberg deformation
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** Select different block types, each behaves uniquely, creates strategic depth.

---

### US-022: Create welcoming Hearthstone-style art assets
**Dependencies:** US-001, US-015
**Description:** As a player, I want a warm, welcoming visual world that feels like a cozy game - not technical or harsh.

**Art Direction - HEARTHSTONE AESTHETIC:**
- **Style:** Painterly, hand-crafted, soft edges - like Hearthstone's game board world
- **NO harsh lines** - everything should feel organic, rounded, friendly
- **Colors:** Warm amber, soft gold, gentle blues - tavern/fantasy warmth
- **Lighting:** Golden hour glow, soft shadows, firelight warmth
- **Materials:** Soft gradients, no hard edges, chunky/friendly proportions
- **Blocks:** Rounded corners, soft bevels, look like carved wood or friendly stone

**Reference Games:**
- Hearthstone (the game board and world, not the cards)
- Legends of Kingdom Rush
- Clash Royale arenas
- Supercell game aesthetics generally

**The Feeling:**
> "Like you're playing on a cozy tabletop by a warm fireplace"

**What to AVOID:**
- Technical-looking grids with harsh lines
- Cold, sterile, sci-fi aesthetics
- Sharp geometric edges
- Minecraft-style pixel look
- Dark, moody, or intimidating visuals

**Asset Requirements:**
1. Block meshes (6 types) - chunky, friendly, rounded corners, soft bevels
2. Core ("B") - warm glowing orb like a friendly campfire
3. Attack units - cute/charming creatures (not scary)
4. Hex territory - soft boundaries, no visible grid lines, organic edges
5. Particles - warm sparkles, golden dust, soft glow
6. Skybox - sunset/golden hour clouds, soft and dreamlike

**Technical Art Notes:**
- Use SDF (Signed Distance Field) for soft edges in shaders
- Outline shaders should be SOFT, not harsh
- Ambient occlusion should be gentle, not dark
- Color palette generator: warm OKLCH values (hue 30-60 range)

**Goal:** Game feels welcoming, like sitting down at a friendly game table.

**Acceptance Criteria:**
- [ ] All block meshes have rounded corners and soft bevels
- [ ] NO visible harsh grid lines anywhere in the game
- [ ] Core glows with warm amber light (not cold blue/white)
- [ ] Attack units look friendly/cute, not threatening
- [ ] Hex boundaries are INVISIBLE or very subtle organic glow
- [ ] Overall palette is warm: golds, ambers, soft browns, gentle sky blues
- [ ] Soft edge shader for all meshes (no hard silhouettes)
- [ ] Golden hour lighting setup (warm directional, soft ambient)
- [ ] Particle effects use warm colors (orange, gold, soft white)
- [ ] Skybox feels like sunset/peaceful sky
- [ ] Screenshot comparison: should feel like Hearthstone, not Minecraft
- [ ] Typecheck passes
- [ ] Verify in-game: does it feel cozy and welcoming?

**Success looks like:** Show someone the game, they say "that looks cozy/friendly" not "that looks technical"

---

### US-023: Gameplay UI Phase Validation
**Dependencies:** US-017, US-018, US-019, US-020, US-021, US-022
**Description:** Validate gameplay UI integration.

**Acceptance Criteria:**
- [ ] All UI elements render correctly
- [ ] Timer drives phase transitions
- [ ] Block placement costs pixels correctly
- [ ] Hex boundaries clearly visible
- [ ] No UI overlap or clipping
- [ ] Game feels playable (can build, see timer)
- [ ] Typecheck passes

**Success looks like:** Complete build phase experience: fly, build, watch timer, manage pixels.

---

### COMBAT PHASE (Depends on Gameplay)

---

### US-024: Implement attack unit spawning
**Dependencies:** US-007, US-005, US-006
**Description:** As a player, I want to spawn attack units during Attack phase to assault the enemy.

**Spawning Rules:**
- Can only spawn during ATTACK phase
- Costs pixels per unit
- Units spawn at edge of your hex
- Select unit type before spawning

**Goal:** Player can commit resources to attacks.

**Acceptance Criteria:**
- [ ] Create `src/combat/spawning.rs`
- [ ] Can only spawn during Attack phase
- [ ] Unit type selection UI (Basic/Heavy)
- [ ] Click on enemy hex to spawn unit
- [ ] Unit appears at your hex edge
- [ ] Pixels deducted
- [ ] Units visible as simple meshes (cubes for now)
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** Enter attack phase, select unit type, click enemy hex, unit spawns.

---

### US-025: Implement unit pathfinding
**Dependencies:** US-024, US-003
**Description:** As a developer, I need units to path around obstacles toward the enemy core.

**Pathfinding Approach:**
- A* or simple flow field
- Obstacles are placed blocks
- Target is enemy core
- Units navigate around structures

**Goal:** Units intelligently navigate to target.

**Acceptance Criteria:**
- [ ] Create `src/combat/pathfinding.rs`
- [ ] A* implementation for 3D grid
- [ ] Blocks are obstacles
- [ ] Path to enemy core center
- [ ] Path recalculates when blocks destroyed
- [ ] Units follow path smoothly
- [ ] Visual debug: show path lines (toggle)
- [ ] Typecheck passes

**Success looks like:** Unit spawns, finds path around walls, heads toward enemy core.

---

### US-026: Implement combat resolution
**Dependencies:** US-025, US-007
**Description:** As a developer, I need units to damage blocks and cores during Resolution phase.

**Combat Rules:**
- Units that reach blocks deal damage
- Blocks have HP (basic: 1 HP, reinforced: 3 HP)
- Destroyed blocks disappear
- Units that reach core deal damage to core
- Core destroyed = lose

**Goal:** Combat resolves with meaningful damage.

**Acceptance Criteria:**
- [ ] Create `src/combat/resolution.rs`
- [ ] Units deal damage when reaching blocks
- [ ] Block HP system (blocks can take hits)
- [ ] Destroyed blocks removed from grid
- [ ] Mesh updates after destruction
- [ ] Core takes damage when units reach it
- [ ] Core destruction triggers lose condition
- [ ] Visual feedback: damage flashes, destruction particles
- [ ] Typecheck passes

**Success looks like:** Units attack, blocks take damage and break, core can be destroyed.

---

### US-027: Implement strategic AI opponent
**Dependencies:** US-021, US-017, US-026
**Description:** As a player, I want to play against a smart AI that provides a real challenge.

**AI Behavior (Strategic):**
The AI should feel like playing against a thinking opponent, not random actions.

**BUILD Phase Intelligence:**
- Analyzes player attack patterns from previous rounds
- Identifies weak points in its defense
- Uses all block types strategically:
  - Spike traps in common attack paths
  - Shield blocks protecting high-value areas
  - Decoys to misdirect attacks
  - Reinforced walls at chokepoints
- Layered defense (outer decoys, middle spikes, inner reinforced)
- Adapts to player strategy (if player always attacks left, fortify left)

**ATTACK Phase Intelligence:**
- Scouts player defenses to find weak spots
- Coordinates unit types (heavies to break walls, basics to flood)
- Times attacks to overwhelm (mass attack vs trickle)
- Feints (sends decoy attacks to probe, then real attack)
- Targets undefended paths first

**Difficulty Levels:**
- **Easy:** 50% optimal decisions, telegraphs attacks
- **Medium:** 75% optimal, some prediction
- **Hard:** 90% optimal, reads player patterns, adapts
- **Brutal:** Perfect decisions, punishes mistakes

**Goal:** AI that feels fair but challenging, teaches players strategy.

**Acceptance Criteria:**
- [ ] Create `src/ai/strategic_ai.rs` module
- [ ] `AIBrain` struct with memory of past rounds
- [ ] Pattern recognition: track where player attacks from
- [ ] Defense analyzer: find weak spots in own base
- [ ] Attack planner: coordinate multi-unit assaults
- [ ] Block type selection based on strategic value
- [ ] Difficulty enum with behavior modifiers
- [ ] BUILD phase: places blocks based on threat analysis
- [ ] ATTACK phase: sends units based on weakness analysis
- [ ] Adaptive behavior: changes strategy if losing
- [ ] Debug visualization: show AI decision reasoning
- [ ] Unit tests for AI decision logic
- [ ] Typecheck passes

**Success looks like:** AI defends intelligently, attacks weak points, adapts when player changes strategy. Feels like a real opponent.

---

### US-028: Implement win/lose conditions
**Dependencies:** US-026, US-006
**Description:** As a player, I want clear win/lose conditions so the game has stakes.

**Conditions:**
- WIN: Destroy enemy core
- LOSE: Your core is destroyed
- DRAW: Both cores destroyed same round (rare)

**Goal:** Games have satisfying conclusions.

**Acceptance Criteria:**
- [ ] Create `src/game/victory.rs`
- [ ] Detect core destruction
- [ ] Display WIN screen with stats
- [ ] Display LOSE screen with stats
- [ ] "Play Again" button
- [ ] Return to main menu option
- [ ] Stats: rounds survived, blocks placed, units spawned
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** Destroy enemy core, see "VICTORY!" with stats, can rematch.

---

### US-029: Combat Phase Validation
**Dependencies:** US-024, US-025, US-026, US-027, US-028
**Description:** Validate complete combat loop.

**Acceptance Criteria:**
- [ ] Full game loop works: Build → Attack → Resolution → Build...
- [ ] AI provides reasonable challenge
- [ ] Units path correctly around obstacles
- [ ] Combat damage resolves correctly
- [ ] Win/lose triggers at correct times
- [ ] Game can be completed (won or lost)
- [ ] Typecheck passes

**Success looks like:** Play a complete game against AI, experience tension, win or lose.

---

### POLISH PHASE (Final Integration)

---

### US-030: Create sample benchmark scene
**Dependencies:** US-016
**Description:** As a developer, I need a benchmark scene demonstrating 20,000+ FPS capability.

**Scene Design:**
- Massive voxel structure (100,000+ blocks)
- Stress test for rendering pipeline
- FPS counter and performance stats
- Toggle between quality levels

**Goal:** Prove the Vercidium optimizations achieve target performance.

**Acceptance Criteria:**
- [ ] Create `src/benchmark/scene.rs`
- [ ] Generate procedural structure with 100,000+ blocks
- [ ] Display FPS, draw calls, vertex count
- [ ] FPS > 1000 in release build (modern GPU)
- [ ] Document actual FPS achieved on test hardware
- [ ] Screenshot for documentation
- [ ] Typecheck passes

**Success looks like:** Launch benchmark, see 100K+ blocks at 1000+ FPS, impressive demo.

---

### US-031: Main menu and game flow
**Dependencies:** US-028, US-006
**Description:** As a player, I want a main menu to start games.

**Menu Options:**
- New Game (vs AI)
- Sandbox Mode (no timer, no AI, just build)
- Settings (resolution, FPS cap)
- Quit

**Goal:** Polish entry point for the game.

**Acceptance Criteria:**
- [ ] Create `src/ui/menu.rs`
- [ ] Main menu with options
- [ ] "New Game" starts game vs AI
- [ ] "Sandbox" starts free build mode
- [ ] Settings screen (basic: resolution, audio volume)
- [ ] ESC returns to menu mid-game (with confirm)
- [ ] Clean visual design (Hogwarts aesthetic)
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** Launch game, see pretty menu, click New Game, play.

---

### US-032: Audio feedback system
**Dependencies:** US-017, US-026
**Description:** As a player, I want audio feedback for actions so the game feels responsive.

**Audio Events:**
- Block placement: satisfying "click"
- Block destruction: crumble sound
- Unit spawn: whoosh
- Phase transition: bell/chime
- Core damage: alarm
- Victory: fanfare
- Defeat: somber tone

**Goal:** Audio makes actions feel impactful.

**Acceptance Criteria:**
- [ ] Create `src/audio/mod.rs`
- [ ] Bevy audio system integration
- [ ] Placeholder sounds for all events (can be beeps for now)
- [ ] Volume control in settings
- [ ] Spatial audio for 3D events (optional)
- [ ] Typecheck passes

**Success looks like:** Every action has audio feedback, game feels alive.

---

### US-033: Final Integration Validation
**Dependencies:** US-029, US-030, US-031, US-032
**Description:** Final validation of complete Phase 1 prototype.

**Full Test:**
1. Launch game
2. Navigate menu
3. Start new game vs AI
4. Play through multiple rounds
5. Experience combat
6. Win or lose
7. Rematch or quit

**Acceptance Criteria:**
- [ ] Complete game flow works end-to-end
- [ ] No crashes during normal play
- [ ] FPS stays above 60 during gameplay
- [ ] Benchmark scene achieves 1000+ FPS
- [ ] Audio plays correctly
- [ ] All UI is functional
- [ ] Build phase feels fun (per gameplay doc)
- [ ] Attack phase has tension (per gameplay doc)
- [ ] AI provides challenge without being impossible
- [ ] `cargo clippy` passes
- [ ] `cargo test` passes
- [ ] Release build runs smoothly
- [ ] Typecheck passes

**Success looks like:** Someone can download, build, and enjoy a complete game session.

---

## Non-Goals (Phase 1)

- **Multiplayer/Networking**: Local only (multiplayer is Phase 3)
- **Crafting/Upgrades**: No tech tree (future feature)
- **Mobile support**: Desktop only
- **Tutorial**: Learning by doing (tutorial later)

## Technical Considerations

### From stikcer_game (reference)
The existing `../stikcer_game/` has:
- `src/render/` - Rendering patterns to reference
- `src/combat/` - Combat system patterns
- `src/physics/` - Physics integration
- `src/game/orchestrator.rs` - Game loop patterns

Can reference these for Bevy patterns but rewrite for new architecture.

### Performance Targets
- Release build: 1000+ FPS on benchmark scene
- Gameplay: >144 FPS at all times
- 100,000+ blocks renderable
- <16ms frame time during combat

### Directory Structure
```
sticler/
├── src/
│   ├── main.rs
│   ├── hex/           # Hex coordinate system
│   ├── grid/          # Dual-grid (Stalberg)
│   ├── render/        # Vercidium optimizations
│   ├── building/      # Block placement
│   ├── combat/        # Units, pathfinding, damage
│   ├── economy/       # Pixel system
│   ├── game/          # State machine, victory
│   ├── ai/            # Simple AI
│   ├── ui/            # HUD, menus
│   ├── audio/         # Sound effects
│   └── benchmark/     # Performance testing
├── assets/
│   ├── meshes/        # Block models
│   ├── shaders/       # Custom shaders
│   └── sounds/        # Audio files
├── Cargo.toml
└── README.md
```
