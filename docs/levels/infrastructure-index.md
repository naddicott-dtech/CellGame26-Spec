# Shared infrastructure index

One line per shared system a level relies on, with where it is defined. Every level spec's "Shared infrastructure used" section ([`spec-template.md`](spec-template.md)) checks this list. Written in [Define the shared level defaults and the infrastructure index](../tickets.md#ticket-79).

**The rule:** builders use these systems. A spec never describes a level-private copy (a "state machine at home"). If a level needs something a system lacks, the spec names an **extension** to that system, and the extension gets its own ticket.

| Shared system | What it gives a level | Defined in |
|---|---|---|
| **Game Shell state machine** | Screens (title, level select, cutscene, level in play, results), a fresh core per level, Pause with stacking reasons, a hidden tab, and chaos-safe screen changes | `architecture.md`: Game Shell; pause reasons in [shared defaults: Pause](shared-defaults.md#pause-how-to-play-and-the-encyclopedia) |
| **Objectives and the minimum beat** | One Objective at a time, introduction acknowledged, the minimum beat, completing once, and the completion linger | `architecture.md`: Simulation Core; [shared defaults: Objectives](shared-defaults.md#objectives-completion-and-the-linger) |
| **Story Hold** | Gameplay frozen on the triggering tick, with only the beat's own motion; fast-forward of familiar holds on a Restart | `architecture.md`: Simulation Core; [shared defaults: Story Holds](shared-defaults.md#story-holds) and [Restart](shared-defaults.md#restart) |
| **Game Events** | Ordered, stamped, serialisable events that the renderer, UI and Session Log react to; the replay guarantee | `architecture.md`: Game Events |
| **Loss Routes and Starvation** | The shared ways to lose, each possible, impossible or unknown; the Starvation Line, grace, countdown and first-time card | [`levels.md`: the shared end-of-level rules](../levels.md#the-shared-end-of-level-rules) |
| **Win-impossible predicates and Dead Ends** | Irreversible typed predicates with reasons, evaluated after state settles; the draw when every Loss Route is impossible | [`levels.md`: Winning is impossible](../levels.md#winning-is-impossible) and [Dead End](../levels.md#dead-end) |
| **The Restart button** | Restart under the broken Objective and in the Pause menu; a fresh attempt with a new seed; the cause and one hint after a loss | [`levels.md`: How a level ends](../levels.md#how-a-level-ends); [shared defaults: Restart](shared-defaults.md#restart) |
| **The idle watchdog** | Nudges on a gap schedule, reset by real progress; semantic highlight targets; off-screen arrows | `architecture.md`: Simulation Core; [shared defaults: the idle watchdog](shared-defaults.md#the-idle-watchdog) |
| **Tuning** | Every number as typed content, with a dev-build override; shared values in one table | `architecture.md`: Content; [shared defaults: Tuning](shared-defaults.md#tuning) |
| **The Level Rating** | Wave Ratings, the weighted average, rounding, "Complete", saving the best rating; the results screen | [`levels.md`: The Level Rating](../levels.md#the-level-rating); [shared defaults: Clocks](shared-defaults.md#clocks-and-the-results-screen) |
| **The Session Log** | The record of what a student did, answered and reached, with the clocks and refusal counts | [Define the Session Log and save format](../tickets.md#ticket-15); `architecture.md`: Persistence |
| **The spark preview and refusal** | The one core calculation for a pull (target, cost, affordability, validity), the spark, the dashed stub, the refusal cues and `pseudopod_refused` | [shared defaults: the pseudopod spark preview and refusal](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) |
| **The adapters** | Input (DOM pointer and keyboard, click versus drag, screen scale as data), particles, text, assets and textures, teardown | `architecture.md`: Renderer; traps in `pixi-trap-catalogue.md` |

Also shared, and checked when a level touches them:

| Shared system | Defined in |
|---|---|
| **Selection and touch targets** | `architecture.md`: Simulation Core; [shared defaults: Touch and selection](shared-defaults.md#touch-and-selection) |
| **The world edge** | [shared defaults: The world edge](shared-defaults.md#the-world-edge) |
| **Left-behind gems** | [shared defaults: Gems not drawn from a pool](shared-defaults.md#gems-not-drawn-from-a-pool) |
| **Persistence** | `architecture.md`: Persistence |
| **Balance Runs and the stuck-state search** | `architecture.md`: Balance testing; [`levels.md`: Testing](../levels.md#testing); `docs/testing.md` |

Baseline simulation systems, first used in Level 2. Their rules come from the catalogue, so they need no design ticket, but they are shared systems owned by the build and never implemented privately inside a level ([Specify Level 2 — Nucleus](../tickets.md#ticket-63)):

| Shared system | Defined in |
|---|---|
| **Purchases** (costs, batches with partial fills, caps counting ordered units, nucleolus pores) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §1.4 and §F (D18, D19); [`level-2.md`](level-2.md) |
| **The production pipeline** (RNA, ribosomes, enzymes, temporary nucleotides) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §3.2 |
| **Virus behaviour and waves** (spawning, hijacking, escape, dormancy, defeat, rewards) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §5 |
| **Membrane health and self-repair**, shown with a shared health display | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §2.3 |

First used in Level 3 ([Specify Level 3 — Membrane](../tickets.md#ticket-64)):

| Shared system | Defined in |
|---|---|
| **Small-unit recycling** (charge now, building blocks back when the unit's recycling finishes; group runs with honest partial results) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §1.4 and §I; [`level-3.md`](level-3.md) |
| **Membrane growth and shrinking**, and the **lens size**, which is core geometry read by the pull clamp, the spark preview, spawn placement and the camera | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §2.1, §2.2 and §I (D3 and D24 fixed in [`level-3.md`](level-3.md)) |
| **Hydrogen peroxide** (production, targeting, damage) and **peroxisomes** | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §1.3, §3.1, §6 and §I |
| **DNA repair enzymes**, reusable for several repair jobs (a Deviation; the value is in [shared Tuning](shared-defaults.md#tuning)) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §3.1 and §I; [`level-3.md`](level-3.md) |
| **Producer damage** (a damaged mitochondrion or chloroplast stops producing) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §3.4 |

First used in Level 4 ([Specify Level 4 — Golgi and lysosomes](../tickets.md#ticket-65)):

| Shared system | Defined in |
|---|---|
| **The Golgi** and the lysosome's production route through it | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §3.1, §3.2 and §J; [`level-4.md`](level-4.md) |
| **Lysosomes** (present from growth, ready after it; eating viruses; destroyed by one peroxide hit) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §3.1, §5.2 and §J |
| **Organelle recycling by lysosomes** (the digestion vesicle, borrowed lysosomes, the acid, the refund, Cancel Recycle; waiting shown as progress) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §1.4 and §J; [`level-4.md`](level-4.md) |
| **Mitochondrion division** (no health check; a full-health daughter) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §1.4, §3.1 and §J |
| **Invader viruses** | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §5.2 and §J |

First used in Level 5 ([Specify Level 5 — Chloroplast](../tickets.md#ticket-66)):

| Shared system | Defined in |
|---|---|
| **The chloroplast** (production scaled by sunlight, peroxide, Divide without a health check, recycling by lysosomes); its cycle is [under verification](../tickets.md#ticket-100) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §1.3, §3.1, §6 and §K; [`level-5.md`](level-5.md) |
| **Sunlight**, a level-wide value | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §6 and §K |
| **Auto-replacing peroxisomes** (the ER's toggle; one full-price attempt per wear-out, D25 fixed; missed attempts reported) | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §3.1 and §K; [`level-5.md`](level-5.md) |
| **Defensins** (fused count, strength diluted by membrane size, the shield display) and **vesicle-wrapped viruses** | [`baseline-mechanics.md`](../research/baseline-mechanics.md) §2.5, §5.1 and §K; [`level-5.md`](level-5.md) |
| **The Class Minimum in the save** | [Persistence extension: save the Class Minimum](../tickets.md#ticket-101) |

Shared interaction contracts that finish existing parts of the architecture. Each preserves the catalogue's gameplay rules and timing:

| Contract | Ticket |
|---|---|
| Camera zoom, scroll and scripted moves | [Camera zoom, scroll and scripted moves: the shared interaction contract](../tickets.md#ticket-82) |
| Action Panel purchases | [Action Panel purchases: the shared interaction contract](../tickets.md#ticket-83) |
| Virus wave presentation | [Virus wave presentation: the shared interaction contract](../tickets.md#ticket-84) |
| Group selection and group actions | [Group selection and group actions: the shared interaction contract](../tickets.md#ticket-87) |
| Organelle Move and Toggle | [Organelle Move and Toggle: the shared interaction contract](../tickets.md#ticket-88) |
| Concurrent waves (an extension of wave presentation) | [Concurrent waves: presenting and acknowledging waves sent together](../tickets.md#ticket-93) |
