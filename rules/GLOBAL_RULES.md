# Project Rules for Sticler

## Performance is Non-Negotiable
- Every rendering decision must consider the 20k+ FPS target
- Profile early, profile often
- If a feature hurts performance, find a better way

## Stalberg Grid Principles
- ALWAYS use quadrilaterals, never triangles for grid cells
- Dual-grid corners define types, not tile centers
- Mesh deformation uses vertex handles on bounds
- Relaxation algorithm runs on grid generation

## Code Quality
- [ ] Typecheck passes
- [ ] No runtime panics in Rust (use Result/Option)
- [ ] GPU code reviewed for performance
- [ ] Memory allocations minimized in hot paths

## Before Completion Checklist
- [ ] FPS counter shows 20k+ in test scene
- [ ] Grid looks organic (not blocky)
- [ ] Player can fly and place blocks
- [ ] Base can be destroyed
- [ ] README.md exists with build instructions

## Worker Guidelines
- Check `context/` folder for reference materials
- Reference `../stikcer_game/` for existing patterns
- Prioritize gameplay feel over feature count
- Test with split-time mode frequently
