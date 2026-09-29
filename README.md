# CellGame26 specs (reference copy)

The level-by-level design for CellGame26, a web remake of *CellCraft* used to teach biochemistry and cell biology at d.tech. It's shared as a reference for other people building their own version, and it's full of teacher judgment: what each level must teach, where the original game was changed and why (each change is called a *Deviation* and carries a *Pedagogy Rationale*), and how a level may be won, lost or stuck.

**Snapshot** of commit `feb0559f4e96`, taken 2026-09-29. The specs are still being written: Levels 6–8 aren't specified yet, and open questions are marked as such.

## Start here

1. [CONTEXT.md](CONTEXT.md): the vocabulary. Every capitalised term in the specs is defined here.
2. [docs/levels.md](docs/levels.md): the level list and the rules every level shares.
3. [docs/levels/](docs/levels/): one spec per level, plus the shared defaults and the template they follow.

## Everything included

- [CellGame26](CONTEXT.md)
- [Learning targets](docs/learning-targets.md)
- [The v1 level list](docs/levels.md)
- [Shared infrastructure index](docs/levels/infrastructure-index.md)
- [Level 1 — Pseudopod (working title)](docs/levels/level-1.md)
- [Level 2 — Nucleus (working title)](docs/levels/level-2.md)
- [Level 3 — Membrane (working title)](docs/levels/level-3.md)
- [Level 4 — Golgi and lysosomes (working title)](docs/levels/level-4.md)
- [Level 5 — Chloroplast (working title)](docs/levels/level-5.md)
- [Shared level defaults](docs/levels/shared-defaults.md)
- [Level spec template](docs/levels/spec-template.md)
- [Baseline mechanics catalogue](docs/research/baseline-mechanics.md)
- [Story and cast](docs/story.md)
- [Planning tickets referred to in these specs](docs/tickets.md)

## What isn't here

- The planning tracker. Specs link to its tickets by name; [docs/tickets.md](docs/tickets.md) says where each stood.
- The engineering docs (architecture, testing, rendering tests), so some references to them are plain text.
- Notes transcribed from the original game's walkthrough videos. Watch the playlist itself for how the original looks and plays.

## Clean room

*The Baseline* in these docs is the open-source CellGame fork of CellCraft. [baseline-mechanics.md](docs/research/baseline-mechanics.md) describes its mechanics and numbers in our own words. Nothing here copies its code, level files, in-game text, art or music, and a remake built from these docs shouldn't either.

## License

MIT, like the game. See [LICENSE](LICENSE).
