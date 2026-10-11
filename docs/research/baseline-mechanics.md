# Baseline mechanics catalogue

Resolves [#3](../tickets.md#ticket-3). Terms follow `CONTEXT.md`:
- **Baseline** is the fork's source for mechanics, numbers and lose conditions, together with the draft AC doc for player-visible flow and feel.
- A **Deviation** is any deliberate difference from the Baseline. Each one needs a Pedagogy Rationale.

## Sources and clean-room note

- **Fork.** [larsiusprime/CellGame--Open-Source-fork-of--CellCraft--](https://github.com/larsiusprime/CellGame--Open-Source-fork-of--CellCraft--).
  - Licences: GPL-3.0 code, CC BY-SA 3.0 art, and separately licensed music.
  - It was read for reference only. Citations take the form "file, routine or constant". Line numbers are left out because the `.as` files use old-Mac CR line endings.
  - Level data is in `level_00.xml` … `level_07.xml`. They are embedded in the build through `Director.as`, and file N is always level slot N.
- **Draft AC doc.** Google Doc "CellCraft Remake — Feature Specification & Acceptance Criteria" (id `1LVyasw…k6g`), read only. It is cited as *AC Pass N §x*.
  - The doc is pasted chatbot output built from walkthrough videos.
  - Passes 1–2 carry citations and match the fork closely.
  - Passes 3–7 have **no citations**. They contain many mechanics and numbers that do not exist in the fork (see Conflicts).
  - Some formula values were lost in the export; they are shown as *[blank]*.
- **Clean-room rules for this document.**
  - Everything below describes behaviour in our own words.
  - Numbers are recorded as facts.
  - No code, XML, dialogue, tutorial text or encyclopedia text is reproduced.
  - Each tutorial beat is summarised only by what it teaches.
- **Time units.** The fork counts **frames**. Its code comments equate 30 frames with 1 s, and the level countdown ticks once per 30 frames. The real stage frame rate lives in the binary `.fla`, which was not read.
  - Every "≈ s" figure below assumes 30 fps.
- **Resource order.** Cost vectors are written `[ATP, NA, AA, FA, G]`, the fork's own order.

## Contents

1. Resources and the economy
2. Membrane, movement and cytoskeleton
3. Organelles and products
4. Lose conditions and failure states
5. Viruses and waves
6. Environmental meters and hazards
7. Level trigger vocabulary
8. Level list
- A. Conflicts (fork vs AC doc)
- B. Gaps (neither source covers)
- F. Fork defects (not Baseline intent)
- G. Corrections from the level-list cross-check
- H. Corrections from the Level 2 spec
- I. Corrections from the Level 3 spec
- J. Corrections from the Level 4 spec
- K. Corrections from the Level 5 spec
- L. Corrections from verifying the mitochondrion's cycle

## 1. Resources and the economy

### 1.1 The five resources

The fork always lists resources in the order **ATP, NA, AA, FA, G**. Every cost below uses that order as `[ATP, NA, AA, FA, G]`.

| Resource | Meaning | Hard cap in play | Notes |
|---|---|---|---|
| ATP | Energy currency | **10,000**. The only cap actually enforced (Engine.as, max-ATP field and the max-resource clamp; see §F) | Spent on almost everything, and charged when recycling. |
| NA (nucleic acids / nucleotides) | Raw material for RNA | Nominally 100 (Cell.as, resource max constants), **not enforced** | Mostly a temporary cost: each RNA strand returns its NA when it degrades. |
| AA (amino acids) | Protein building blocks | Nominally 2,000 (200 × AAX), **not enforced** | All AA costs are written as multiples of `AAX` = 10 (Costs.as). |
| FA (fatty acids) | Lipids | Nominally 100, **not enforced** | Made from surplus glucose, or found in the world. The mitochondrion's backup fuel. |
| G (glucose) | Fuel | Nominally 50. Enforced *indirectly*: surplus above the cap is converted to FA or burned (see 1.3) | Found in the world, or made by chloroplasts. |

- **Caps that do nothing.** Only the ATP cap works; the NA/AA/FA/G caps are dead code (§F, D1).
  - A per-cell-volume cap (area × concentration constants in Cell.as) exists but is commented out. The flat constants above are used instead.
  - The pickup code has a "saturation" check (GoodieGem.as) that is also commented out.
- **Spending is all-or-nothing per unit.** A unit goes through only if every resource in its cost is available (Engine.as, spend). Nothing is ever partially paid. **A batch is not all-or-nothing** (corrected, §H): a "Buy 5" click buys one unit at a time until one fails, keeps the units already bought, then shows the failure alert and reports the action as failed (§F, D18).
- **Starting resources are set per level** (level XML, the resources element under levelInfo; applied by Engine.as, start-resources routine). See §8.

### 1.2 Where resources come from (world pickups)

Source: com/woz/WizardOfOz.as, "goodie" encounter logic; WorldCanvas.as, goodie-gem creation and collection.

- **Hidden pools.** The level defines a hidden pool per resource type (level XML, the goodies stuff entries). Each entry has a starting `count` and a `spawn` probability.
- **Pools start from the level file, off by default.** Every pool begins at count 0, spawn 0 and inactive; the level's goodies entries then overwrite count, spawn and the active flag (WizardOfOz.as, init and basic-goodie maker). A level trigger (`activate_stuff`) can switch a pool on later (WizardOfOz.as, activate-stuff).
- **Pool regrowth.** Each active pool grows by 1 with probability `spawn` on every 16th frame (about 0.53 s). The frame counter must *exceed* 15 before it fires (WizardOfOz.as, tick-time constant 15 and tick-time routine; §F, D16). **Added (§J):** regrowth has no ceiling; a pool keeps growing while it is active.
- **Gems appear as the cell moves.** The game rolls for each active pool whether a pickup gem appears just outside the visible lens.
  - **Cadence.** A "movement tick" is one call of the cell-move handler (Cell.as, on-cell-move → WizardOfOz.as, tick-space). During a pseudopod, that is one call per frame of the contraction phase, when the cell steps 12 px (§2.4). The roll happens on every 6th tick, because the counter must exceed 5 (WizardOfOz.as, tick-space constant 5). That works out to one roll per about 72 px of cell travel.
  - **Chance per roll** = (pool count × gem area) ÷ (2 × lens area). Gem area is a fixed disc of radius 50 px, π × 50² ≈ 7,854 px². Lens area is π × lens radius² (WizardOfOz.as, goodie-area constant, update-lens-size and goodie-encounter tick). Written out, the chance is count × 1,250 ÷ lens radius². At the starting lens radius of about 1,191 px (§2.1), that is about **0.088% per gem in the pool**. A 200-gem pool gives about 17.6% per roll.
  - **Pool debit.** When a roll succeeds, the pool shrinks by 1 *before* placement is attempted (WizardOfOz.as, encounter-goodie).
  - **Placement.** The gem goes at a random angle, at distance (lens radius + the gem graphic's larger dimension) from the centrosome (WorldCanvas.as, encounter-goodie-gem). The graphic's size lives in the `.fla` art, which was not read.
  - **Fork quirk.** If that spot would lie outside the world boundary circle, no gem is made. The pool unit is still gone (§F, D15).
  - A consequence: **resources appear only while you move.** Standing still spawns nothing.
- **Gems do not drift.** Pickup gems are static in world space. Only the cell moves relative to them (GoodieGem.as has no motion code; WorldCanvas.as, encounter-goodie-gem sets no movement).
- **Gem collision radius.** Every gem has a radius of **25 px**, whatever its value or size tier (GoodieGem.as, constructor). The ×2/×4 tiers only change the number shown on the gem.
- **Gem sizes.**

  | Resource | Base gem value |
  |---|---|
  | NA | 1 |
  | AA | 50 |
  | FA | 2 |
  | G | 2 |

  - 5% of gems are ×4 (big).
  - Another 25% are ×2 (medium).
  - The remaining 70% are ×1 (small).
- **Collecting.** A gem is collected when the membrane touches it.
  - **The test.** For each membrane segment (node to next node), the game finds the closest point on that segment to the gem centre. If that point is closer than the gem radius, the gem is collected. Only gems in the object-grid cells next to each node are tested (Membrane.as, collision test and the circle-vs-segment helpers).
  - It is a touch test, not an "inside the polygon" test.
  - The full value is banked at once; the saturation/partial-pickup code is commented out (GoodieGem.as, touch handler).
- **Missed gems.** A gem goes back into its hidden pool when its distance from the centrosome exceeds **2 × the lens radius**. It is returned as +1 to the pool count, whatever its value (WorldCanvas.as, far-boundary multiplier 2.0 and check-bounds; WizardOfOz.as, absorb-goodie-gem).
  - The check runs only when the cell moves.
  - It also applies to gems placed by a level's `spawn_object` action. Those gems then join the pool of their resource type.
- **Virus rewards: none per kill** (corrected, §H). Killing a virus pays nothing. The reward constants in Costs.as are used only by the wave-defeat payout (§5.3):
  - Injector: NA 0.02, AA 2.5, FA 0.25.
  - Invader: NA 0.025, AA 2.5, FA 0.25.
  - Infester: NA 0.025, AA 2.5, FA 0.25.

### 1.3 Conversion rates (metabolism)

Frame timings are Baseline frames at **30 fps**. **Corrected (§H, §L):** the Flash document declares 30 fps, and the compiled game's header says 30.0. The code comments also equate 30 frames with "1 second" (Ribosome.as, Golgi.as, Membrane.as).

| Process | Input → output | Rate / condition | Source |
|---|---|---|---|
| Mitochondrion, normal | 1 G → **38 ATP** | **Verified (§L): a Production Cycle of 97 frames (about 3.23 s), credit to credit,** in two phases. **Counting, 46 frames:** the counter must pass 45; on the 46th frame 1 G is charged. **Processing, 51 frames:** the processing animation plays with the counter frozen, and 38 ATP is credited on its last frame, 51 frames after the charge. **The first cycle** starts from a random count, so the first credit comes 53–97 frames (about 1.8–3.2 s) after the mitochondrion starts counting. While toggled on and undamaged | Mitochondrion.as, input/output arrays, produce time and starting count; ProducerObject.as, produce loop; the processing animation's length (§L) |
| Mitochondrion, backup fuel | 1 FA → **108 ATP** | Only when there is no G **and** ATP is below 250. The same animation, so the same 97-frame cycle (§L) | Mitochondrion.as, alternate-burn override |
| Cytoplasm "anaerobic" glycolysis | 1 G → **2 ATP** | **Corrected (§L):** once per 61 frames (the counter must pass 60; about 2.03 s), credited at once with no animation, **only when the cell has zero mitochondria** | Cell.as, produce tick |
| Surplus glucose → fat | 4 G → **1 FA** | Once per 61 frames (the counter must pass 60), when G exceeds its cap (50) by at least 4 and fat processing is on. **Corrected (§H): fat processing defaults on**, and runs whenever cytoplasm processing is on | Cell.as, glucose-to-FA routine |
| Surplus glucose burned | 4 G → nothing | Once per 61 frames, when G is over cap and fat processing has been switched off | Cell.as, excess-glucose burn |
| Chloroplast | nothing → **1 G** | **Corrected (§K):** not once a second. The counter must pass int(30 ÷ sunlight) frames; then the process animation plays with the counter frozen, and 1 G is credited at its **end**: about 31 + 52 ≈ **83 frames per G at sunlight 1** (medium confidence; under verification). At 0 it stops. The 45-frame default is never observable (each chloroplast takes the level's sunlight when created). Costs nothing to run | Chloroplast.as; ProducerObject.as, efficiency setter and produce loop |
| "Brown fat" mode (mitochondrion) | 1 G → heat, not ATP | Each burn adds +0.25 to temperature instead of making ATP | ProducerObject.as, heat-per-burn constant |
| RNA degradation | RNA → its NA value back | When any non-viral RNA strand finishes or dies | Cell.as, RNA kill routine |

- **Toggles.** Mitochondria and chloroplasts can be switched off and on (the TOGGLE action).
- **Free radicals.** Each production cycle can create free radicals; see §6.
  - The first production cycle of each organelle never makes one.
  - Chloroplasts make 1 radical per cycle.
  - Mitochondria accumulate 0.5 per cycle, so 1 radical every 2 cycles.
  - Radicals only appear when the level turns them on.

### 1.4 Make / sell cost table

Source: Costs.as, one constant per item. **Sell** means recycle.

- **What recycling pays.** The ATP number in a sell row is *charged*. The NA/AA/FA/G numbers are *refunded* at full value.
- **Full refund, not 75%.** The declared 75% refund constant is never used (§F, D2), so the sell rows are the real values.
- **Hidden RNA surcharge on "Make".** Items built from an RNA blueprint carry an extra NA charge on top of the listed price (Costs.as, RNA-cost helper).
  - The surcharge is 1 NA per started block of 50 AA in the item's cost (`AA_PER_NA` = 5 × AAX).
  - It is refunded when the RNA degrades, which is why it does not appear in the sell price.

| Item | Make [ATP, NA, AA, FA, G] (listed) | Effective make incl. RNA NA | Sell / recycle [ATP charged; rest refunded] | Where bought | Order size / cap |
|---|---|---|---|---|---|
| Ribosome | [2, 1, 10, 0, 0] | same (no RNA) | [1, 1, 10, 0, 0] | Nucleus | 5 per click; cap 25 |
| Slicer enzyme | [5, 0, 10, 0, 0] | [5, **1**, 10, 0, 0] | [3, 0, 10, 0, 0] | Nucleus | 5 per click; cap 50 |
| DNA repair enzyme | [50, 0, 100, 0, 0] | [50, **2**, 100, 0, 0] | [50, 0, 100, 0, 0] | Nucleus | 1 per click; intended cap 25 (not enforced; §F, D5) |
| Lysosome | [15, 0, 25, 1, 0] | [15, **1**, 25, 1, 0] | [8, 0, 25, 1, 0] | **Golgi** | 2 per click; cap 25 |
| Peroxisome | [50, 0, 50, 2, 0] | [50, **1**, 50, 2, 0] | [25, 0, 50, 2, 0] | **ER** | 1 per click; cap 10; ER has an auto-replace toggle |
| Membrane (1 node) | [100, 0, 50, 5, 0] | [100, **1**, 50, 5, 0] | [50, 0, 50, 5, 0] | ER | 1 per click; max 25 nodes |
| Defensin | [100, 0, 50, 1, 0] | [100, **1**, 50, 1, 0] | [50, 0, 50, 1, 0] | Golgi | 1 per click; cap 50 |
| Toxin | [50, 0, 25, 1, 0] | [50, **1**, 25, 1, 0] | [25, 0, 25, 1, 0] | (not on any default action list; see §8) | toxin-level cap 100 |
| Generic vesicle | [10, 0, 10, 5, 0] | n/a | [4, 0, 10, 5, 0] | not player-buyable (unused helper) | n/a |
| Mitochondrion (divide) | [250, 5, 100, 25, 10] | n/a | [100, 5, 100, 25, 10] | select mitochondrion → Divide | cap 10 |
| Chloroplast (divide) | [200, 5, 100, 25, 10] | n/a | [100, 5, 100, 25, 10] | select chloroplast → Divide | cap 10 |
| Protein glob (found object) | not buildable | n/a | [50, 0, 1000, 0, 0] | recycle only | n/a |
| RNA strand | [0, 1, 0, 0, 0] | n/a | [0, 1, 0, 0, 0] | internal | n/a |
| Viral RNA | [0, 0.25, 0, 0, 0] | n/a | [0, 0.25, 0, 0, 0] | internal | n/a |
| Virus "make" (internal, used when a hijacked cell spends to spawn virions) | injector 5 ATP; invader 10; infester 20 | n/a | injector refunds 2 ATP plus the NA of its 1 RNA; others 0 | n/a | n/a |

Notes on the table:
- **Order sizes.** They come from Act.as, order-multiplier constants.
  - Ribosome batch size: see §F, D4.
- **Queued builds.** Paid-for units are queued as "ordered" and delivered one at a time through the production pipeline (§3.2).
- **Ribosome pore requirement.** Ribosomes need a free nucleolus pore to be ordered.
- **Organelle movement costs.** Charged continuously while an organelle floats to a new spot. The rate is per 500 px, which the game calls 10 µm (Costs.as, per-type move constants; CellObject.as, move-cost-by-speed):

  | Organelle | ATP per 10 µm |
  |---|---|
  | Ribosome | 1 |
  | Lysosome | 2.5 |
  | Peroxisome | 10 |
  | Slicer | 0.1 |
  | Mitochondrion | 50 |
  | Chloroplast | 50 |

  - If ATP runs out mid-move, the move stops.
- **Recycling needs lysosomes.** Selected small units are recycled directly, paying the ATP charge up front (Cell.as, start-recycle). Anything else is "eaten" by the nearest lysosome.
  - Mitochondria and chloroplasts are wrapped in a large digestion vesicle. Enough lysosomes fuse with it to bring its interior to acid pH (PH.as, lysosomes-needed formula), and the acid then kills the contents (BigVesicle.as).
  - **Corrected (§J):** for a mitochondrion or chloroplast, the ATP charge is taken at the click, and the organelle stops producing at once. The vesicle calls the nearest ready lysosomes, up to the number needed: that number is derived from the organelle's drawn size, about 8 for a mitochondrion (possibly 9). **Nothing is digested until all have fused.** With too few, the vesicle waits, calling again and showing a "need lysosomes" alert every 61 frames. Once they're all in, acid does about 11.8 HP every 31 frames. When the organelle dies, its refund is credited after a 31-frame recycle animation, and **the same number of lysosomes bud back out**: they are borrowed, not used up. **Cancel Recycle** releases the organelle and returns the lysosomes, but not the ATP; the organelle keeps any acid damage already taken (nothing heals it).
  - For other things eaten directly by a lysosome, the earlier note (recycling fails with an alert when there are no lysosomes) is unverified.

## 2. Membrane, movement and cytoskeleton

Sources: Membrane.as, MembraneNode.as, Cytoskeleton.as, Cell.as, Costs.as.

### 2.1 Structure

- **Nodes and springs.** The membrane is a ring of spring-linked nodes pulled toward the centrosome.
  - It starts at **15 nodes** on a radius of 400 px.
  - The maximum is **25 nodes** (Membrane.as, starting/max node constants).
- **Rest lengths.**
  - **Node spacing.** The resting gap between neighbouring nodes is the chord of the starting ring: 2 × 400 × sin(12°) ≈ **166.3 px**. It is measured once at start and never changes (Membrane.as, activate-nodes; MembraneNode.as, node-rest statics).
  - **Effective radius.** The game uses nodes × 166.3 ÷ 2π, not the literal 400 (Membrane.as, get-radius). That gives ≈ **397 px** at 15 nodes and ≈ 662 px at 25.
  - **Rest distance to the centrosome.** After any node is added or removed, it is reset to (nodes + 2) × 166.3 ÷ 2π, with the +2 as a fudge (Membrane.as, on-change-nodes; MembraneNode.as, get-springs).
- **Spring rules.** Each frame, a node moves by the sum of three terms (MembraneNode.as, update-dist, churn and do-move):
  - **Each neighbour.** Push or pull of (rest² − d²) ÷ rest² × 15, along the line to that neighbour. It is multiplied by the friction factor 0.5, and applied only when |rest² − d²| > 15 (squared px).
  - **The centrosome.** A flat step of **10 px per frame toward the centrosome**, whenever |d² − centre-rest²| > 15. Note that this pull is inward-only.
  - **Pull pauses.** The centrosome pull is switched off for 5 frames at start and for 30 frames after a node is added (Membrane.as, wait-cent constants).
- **What holds the membrane out.** Six invisible "warble" hard points (Cytoskeleton.as, warble constants; Cell.as, start hard points; HardPoint.as, warble):
  - They sit on a circle of radius 0.56 × membrane radius around the centrosome.
  - Each has a radius of 0.36 × membrane radius. Their radius oscillates by ±30 px at 1 px per frame, which gives the cell its wobble.
  - Any membrane segment that overlaps a hard point, or the pseudopod ball (§2.4), is pushed out by the overlap depth. Both nodes of that segment move (Membrane.as, collision test, circle–segment intersection).
  - Organelle "gravity points" (radius 100) are recorded but do **not** push membrane nodes.
- **Organelles are pushed by the membrane.**
  - **Which ones.** Mitochondria, chloroplasts (ProducerObject.as), lysosomes and peroxisomes have membrane collision switched on. By default a cell object does not collide (CellObject.as, collide flag).
  - **The push.** When a segment overlaps one of these, the object is pushed away from the segment by half the separation vector, and any move it was making is cancelled. Next to a node that is pseudopodding, it also gets that node's displacement (Membrane.as, collision test).
  - **The centrosome** has radius **35 px** (Centrosome.as, auto-radius). It does not collide with the membrane. Nodes are tied to it only by the pull above.
  - **The mitochondrion's radius** is half the larger dimension of its graphic (GameObject.as, auto-radius). The number lives in the `.fla` and was not read.
- **Lens (visible area).** Lens radius = **3 × the membrane's effective radius** (Cell.as, on-membrane-update → World.as, update-mask-size → WizardOfOz.as and WorldCanvas.as, update-lens-size).
  - It is ≈ **1,191 px** at 15 nodes and ≈ 1,985 px at 25 nodes, and it is recomputed whenever the node count changes.
  - Outside the lens, the world is hidden by a circular mask centred on the centrosome (WorldMask.as; World.as, on-cell-move).
- **World and stage.**
  - **Stage.** 640 × 480 (Director.as, stage constants). The play view is shifted to leave room for the HUD (World.as, offset constants 120 and 40, applied as halves).
  - **World units.** Level-file sizes are multiplied by 20 (Terrain.as, scale multiplier). The world boundary is a circle of radius (width ÷ 2) × 20. For slot 0 (width 500) that is **5,000 px**.
  - **Zoom.** Screen scale = 0.1 + 2 × the zoom slider value (Zoomer.as, update). Every level starts at slider value 0.05, which is **scale 0.2**: one world px is 0.2 screen px (Engine.as, level setup). So the starting lens is about 238 screen px in radius.
- **Camera.** While "follow" is on (the default), the view re-centres **exactly** on the centrosome every time the cell moves. There is no easing or lag (World.as, on-cell-move with the follow flag).
  - The HUD centre-camera toggle turns following off or on (Interface.as, start/end follow).
  - Scripted zoom and scroll-to moves are the only eased camera motion. They interpolate linearly over a given number of frames during a faux-pause (Engine.as, faux-pause zoom/scroll commands).
- **Crowding.** Organelles collide with the membrane from inside, and a stretch value is tracked per node. New membrane goes where the ring is most stretched; removed membrane comes from where it is least stretched.

### 2.2 Growth and shrinking

- **Growing (ER → "Make Membrane").**
  - Costs [100 ATP, 1 NA, 50 AA, 5 FA].
  - Sends an RNA blueprint through the ribosome → ER → vesicle pipeline.
  - When the vesicle fuses with the membrane, one node is added and max health rises.
  - At 25 nodes the purchase is refused. The cap counts only fused nodes, not growth in progress (§F, D24). A vesicle that arrives when the membrane is already full is not fused: it refunds **only its 50 AA and 5 FA; the 100 ATP is lost** (corrected, §I) (Cell.as, buy/make membrane).
- **Shrinking ("Take Membrane").**
  - Removes one node, down to the 15-node minimum.
  - It charges 50 ATP and refunds 50 AA and 5 FA (Cell.as, sell-membrane).
  - Quirk: see §F, D3 (the membrane shrinks before the ATP is checked). **Added (§I):** if the charge fails, there is no refund and no take-membrane event, but the routine still reports success and the shrink trigger has already fired.
  - **Added (§I):** a 31-frame cooldown between shrinks; a press during it fails silently. Max HP drops by 10; current HP is lowered only if it is above the new max.
- **Resource caps do not grow with the cell.** The volume-based cap version is commented out (Engine.as, update-max-resources). The only effect of a bigger cell is more membrane HP and more room.

### 2.3 Membrane health and self-repair

- **Max health.** 100 at 15 nodes, plus 10 for every node above 15 (Membrane.as, health-per-node constant and max-health update).
  - The maximum possible is therefore 200 at 25 nodes.
  - A new node also grants 10 health immediately.
- **Automatic repair.** Every 61 frames (the counter must pass 60; corrected, §H), if damaged, the membrane spends **25 ATP to regain 1 HP** (Costs.as, fix-membrane constant; Membrane.as, health update).
  - If ATP is short, it does not heal. There is no player control over this.
- **Colour cue.** The membrane colour blends toward red as health falls.
- **At 0 HP** the cell dies of necrosis; see §4.

### 2.4 Pseudopod (the only locomotion)

- **Cost.** 10 ATP × (distance ÷ 500 px)², which is **quadratic in distance** (Costs.as, pseudopod constant and move distance; Membrane.as, try-pseudopod; Cell.as, pseudopod cost preview).
  - Measured from the cell centre to the click point.
  - Examples: 250 px costs 2.5 ATP; 500 px costs 10; 1000 px costs 40.
- **Affordability.** If you can't afford it, you get an alert and nothing happens.
- **One at a time.** A new pseudopod cancels the old one.
- **Mechanics.**
  - A microtubule extends from near the membrane edge toward the target at speed 12 (Cytoskeleton.as, pseudopod speed).
  - The membrane and contents follow it.
  - The target is clamped so the cell can't leave the visible "lens" radius.
  - When it completes, a "pseudopod_finish" event fires, which level objectives listen for.
- **Input: grab point vs target** (Membrane.as, mouse-down, mouse-move and mouse-up):
  - The player presses on the membrane itself. The press point is the *grab* point.
  - The player drags at least **100 px** in world units (20 screen px at scale 0.2) and releases. The release point is the *target*.
  - The grab point only gates the drag and places the aiming arrow. Direction and cost both come from the **centrosome → target** line.
- **Cost uses the unclamped target.** The cost is worked out from the raw release point (Membrane.as, try-pseudopod) *before* the lens clamp is applied (Cytoskeleton.as, try-pseudopod).
  - A release beyond the lens therefore pays for distance it will never travel.
  - The ATP is charged up front, at launch.
- **Lens clamp.** If the target is farther than the lens radius L from the centrosome, it is moved to centrosome + unit(target − centrosome) × L (Cytoskeleton.as, try-pseudopod).
- **Phase 1: extension.**
  - The tube tip starts at centrosome + unit(direction) × (0.7 × membrane radius − 150). That is ≈ 128 px out at 15 nodes (Cytoskeleton.as, try-pseudopod; recorded centre radius = 0.7 × membrane radius).
  - It advances **12 px per frame** toward the (clamped) target, re-aiming every 5 frames (Microtubule.as, grow-bit and ppod-to).
  - The tip carries an invisible ball of radius **150 px** that shoves membrane nodes outward, so the membrane bulges ahead of the cell (Cytoskeleton.as, pseudopod radius; Membrane.as, collision test).
  - **The cell itself does not move yet.**
- **When "finished" fires.** The extension ends when the tip is within 18 px (1.5 × speed) of the target on both axes. At that moment `pseudopod_finish` fires (Microtubule.as, on-finish → Cytoskeleton.as → Membrane.as, on-finish-ppod). It fires **before** the cell moves, not after.
- **Phase 2: contraction** (Microtubule.as, contract and grow-bit; Cytoskeleton.as, ppod-contract; Centrosome.as and Cell.as, get-ppod-contract).
  - The tip reverses and runs back to its start point at 12 px per frame.
  - On each frame, the centrosome and every cell object except the membrane translate **12 px toward the target** as one rigid group. That includes organelles, hard points and in-flight destinations.
  - The membrane nodes are *not* translated. They catch up through the centrosome pull (10 px per frame) and the hard-point pushes (§2.1).
  - Each contraction frame is one cell-movement tick (§1.2) and re-centres the camera.
- **Net displacement.** The cell ends up moving |target − tip start|. The centrosome therefore stops about (0.7R − 150) px (≈ 128 px) *short* of the clamped target.
  - The furthest possible single move is about L − 128 ≈ 1,063 px at 15 nodes.
  - Both phases take the same number of frames, so a move of D px takes about 2 × D ÷ 12 frames.
- **Cancelling** (Cytoskeleton.as, cancel-pseudopod; Microtubule.as, cancel-ppod).
  - **What cancels.** Only a tube that is still *extending* can be cancelled. A new pseudopod does it, and so do adding membrane and engulfing a floating vesicle (Membrane.as, add-membrane-node and merge-vesicle). A tube that is already contracting keeps pulling.
  - **Cancelling still counts.** A cancel also sends `pseudopod_finish` (Cytoskeleton.as, on-cancel-ppod → Membrane.as, on-finish-ppod).
  - **Near the world edge.** The pseudopod is also cancelled if the tip gets within about 9 px of the world boundary circle, via a boundary test that pads the tip distance by 4 × 150² (Microtubule.as, grow-bit). The test is done in the skeleton's local frame, so the exact threshold is unverified. This is a guard for the world edge, not the lens.
- **Blebs are not implemented.** A bleb cost constant exists (10 ATP), but the bleb functions are commented out (Membrane.as, bleb/ppod-bleb stubs; MembraneNode.as, commented bleb constants).

### 2.5 Defensins in the membrane

- **Effect.** Defensins fuse into the membrane and give a chance to kill incoming viruses.
- **Strength.** Defensin count ÷ (nodes × 1), capped at **0.5 (50%)** (Membrane.as, defensin strength calc).
  - A bigger membrane needs more defensins for the same strength.
  - The number is shown on "shield" icons around the cell.
- **Recycling.** Recycling a defensin costs 50 ATP and refunds 50 AA and 1 FA. **Added (§K):** the defensin is removed at once; slot 4 offers it only after its first wave is defeated.
- **Added (§K):** a defensin counts once its vesicle has **fused** into the membrane (the "make 5" objective counts fused ones). At 15 nodes, 5 defensins give 33% and 8 reach the cap. Wrapped viruses skip the roll (§5.1).

## 3. Organelles and products

### 3.1 Catalogue

"Health" is the fork's max-health value (constructor of each class).

| Thing | What it does (fork) | Health | Actions on it | Source |
|---|---|---|---|---|
| Nucleus | Makes RNA blueprints; holds the DNA. Viral RNA can infest it (§5). DNA repair enzymes heal it, or clear infestation first. If it dies, the level is lost (§4). | 200 | Buy Ribosome ×5, Buy Slicer ×5, Buy DNA Repair ×1 | Nucleus.as; Engine.as, nucleus action list |
| Nucleolus pores | Where ribosomes emerge. A ribosome can only be ordered when a pore is free. | n/a | n/a | Engine.as, buy ribosome |
| Ribosome | Reads one RNA at a time. Enzymes (slicer, DNA repair) and viral products are built in place in the cytoplasm. Vesicle products (lysosome, peroxisome, membrane, defensin, toxin) need the ribosome to dock at the rough ER. About 1 s busy time; 5 s safety timeout. | 25 | none. **Corrected (§I):** the ribosome's action list is empty in every level, so players can never recycle ribosomes. | Ribosome.as, product check / timing constants; Engine.as, setup lists |
| mRNA | Leaves a nucleus pore and waits for or travels to the nearest free ribosome. It degrades after delivering and refunds its NA. Slicers can cut it (only viral RNA is their normal target). | n/a | n/a | RNA.as; Cell.as, generate-mRNA |
| Rough ER | Docking site for ribosomes making vesicle products. Buds peroxisomes and membrane vesicles directly, and other vesicles toward the Golgi (corrected, §I). Player shop for membrane and peroxisomes. | 100 | Make Membrane, Take Membrane, Buy Peroxisome, toggle Auto-Peroxisome | ER.as; Engine.as, ER action list |
| Golgi | Receives ER vesicles (about 2 s processing) and finishes them into lysosomes, defensin vesicles, etc. Player shop for lysosomes and defensins. | 250 | Buy Lysosome ×2, Make Defensin, Take Defensin | Golgi.as; Engine.as, Golgi action list |
| Centrosome | Anchor of the cytoskeleton; the membrane pulls toward it. Not selectable for any action. | 250 | none | Centrosome.as |
| Cytoskeleton / microtubules | Carries the pseudopod and the springs to the centrosome. | n/a | n/a | Cytoskeleton.as |
| Mitochondrion | Glucose → ATP; FA backup (§1.3). Makes free radicals. Can divide (cap 10, counting damaged ones). **Added (§J):** Divide has **no health check**: a damaged mitochondrion, even at 0 HP, can divide, and the daughter has full health. "Brown fat" mode makes heat instead. | 100 | Move, Divide, Recycle, Toggle | Mitochondrion.as |
| Chloroplast | Sunlight → glucose, scaled by the sunlight level. Makes radicals at a higher rate. Can divide (cap 10). **Added (§K):** about 83 frames per G at sunlight 1, 1 radical per cycle (about 2.3× a mitochondrion's rate); stops below 50 HP; Divide [200, 5, 100, 25, 10] with **no health check**, 40 frames, a full-health daughter 75 px away. Obtained only when one floats in (engulfed); never built. | 100 | Move, Toggle, Recycle, Divide (the class's own list is commented out; the Engine list supplies these) | Chloroplast.as; Engine.as |
| Lysosome | Acid vesicle. Digests things it is sent to (recycling, engulfed viruses) and supplies acid to digestion vesicles. Local cytosol pH effects (§6). **Added (§J):** it eats one virus per 80-frame meal and survives; it counts from when it starts growing at the Golgi, about 90 frames before it can act; one radical hit (5) destroys it, refunding 25 AA + 1 FA (D23). | 3 | Move, Recycle | Lysosome.as |
| Peroxisome | Hunts free radicals and destroys them. Takes **1 damage per radical** it absorbs, so it wears out after about 20. **Added (§I):** a radical that *targets* it does the full 5; at 0 health it dies through the recycle path and **refunds 50 AA + 2 FA** (§F, D23); speed 4 px per frame. Invincible radicals are immune. If auto-replace is on, a dead peroxisome is re-ordered automatically; **auto-replace defaults off**. **Added (§K):** only a peroxisome that dies from damage is re-ordered (not one the player recycles), as one full-price purchase through the pipeline, with no retry; a failed purchase shows the normal alert. At the cap of 10 it fails (§F, D25). | 20 | Move, Recycle | Peroxisome.as, arrive-at-object; Cell.as, kill peroxisome |
| Slicer enzyme | Seeks and cuts viral RNA (§5). **Single-use** (added, §H): cutting uses it up and refunds its 10 AA, with no ATP charge. | 1 | Recycle | SlicerEnzyme.as |
| DNA repair enzyme | Waits until the nucleus is damaged or infested, then travels to it. Heals it 5 per application, clearing infestation first. **Added (§I):** it then recycles itself and **refunds its 100 AA** (net cost 50 ATP plus NT in flight; §F, D23). | 1 | Recycle | DNARepairEnzyme.as, heal-value constant |
| Defensin | Embedded in the membrane; anti-virus chance (§2.5). | n/a | via Golgi | Membrane.as |
| Toxin | Made in vesicles. Raises a "toxin level" (0–100) that the level reads (§6). | n/a | n/a | Cell.as, make toxin; Engine.as, buy toxin |
| Protein glob | Found object. Recycle it for 1,000 AA (costs 50 ATP). | 250 | Recycle | ProteinGlob.as |
| Big (digestion) vesicle | Temporary vesicle that wraps an organelle being recycled; lysosomes fuse to acidify it. | n/a | n/a | BigVesicle.as |

### 3.2 Production pipeline

Source: Engine.as, buy/free functions; Cell.as, generate-mRNA and generate-*; Ribosome.as; RNA.as.

1. **Pay.** The player pays the full cost up front, including the RNA's NA. The unit is counted as "ordered".
2. **Transcribe.** An RNA strand emerges from a nuclear pore and finds a free ribosome. If none is free, it waits and keeps polling.
3. **Translate.** What happens next depends on the product:
   - **Enzymes** (slicer, DNA repair) are threaded and released right beside the ribosome.
   - **Vesicle products** send the ribosome to dock at the ER. A protein cloud goes into the ER. **Corrected (§I):** what happens next depends on the product.
     - A **peroxisome** buds straight from the ER; no Golgi step.
     - A **membrane vesicle** buds from the ER and travels straight to the membrane.
     - A **lysosome, defensin vesicle or toxin vesicle** buds from the ER as a small vesicle, travels to the Golgi, and the Golgi (after about 2 s) buds the final product. **Added (§J):** a lysosome then plays a 90-frame grow animation, and it counts toward objectives from the start of that animation.
   - **Ribosomes** do not use RNA. They grow straight out of a nucleolus pore.
4. **Refund.** The RNA degrades and returns its NA.

Pipeline notes:
- **Throughput.** It is limited by the number of free ribosomes, ER docking points and Golgi docking points. There are no explicit build timers beyond the animation and busy times. **Those animation timings are in §M** (the ribosome's "about 1 s busy; 5 s timeout" in §3.1 is dead code there).
- **Hijacking.** Viral RNA uses the same ribosomes (§5), so viruses compete with the player for factories.

### 3.3 Unlocks

- Unlocking is **per level, not global.** Each level's XML hides or shows resources, organelles and individual actions through its trigger actions (§7).
- The levels also use trigger actions to swap in custom action lists.
- The per-level unlock order is in §8.
### 3.4 Damage and organelle failure

Source: GameObject.as, take-damage; ProducerObject.as, produce loop.

- **Damage bands.**
  - Below 50% health an object counts as "lightly damaged".
  - Below 25% it counts as "heavily damaged".
- **Damaged producers stop.** A damaged mitochondrion or chloroplast (light or heavy) **stops producing entirely**.
  - Ordinary damage never kills a producer. It only dies from a "hard kill" (acid pH or necrosis).
- **Healing.** Only two things heal:
  - The membrane repairs itself by spending ATP (§2.3).
  - The nucleus is healed by DNA repair enzymes.
- **Everything else stays damaged.** The player's remedy is to divide a healthy organelle or recycle the damaged one.
- **Centrosome.** It cannot be destroyed (Cell.as, kill-centrosome is a stub).

## 4. Lose conditions and failure states (fork only)

The AC doc names exactly one lose condition, membrane at 0 (AC Pass 2 §5.3). Everything else below comes from the fork.

| # | Condition | What happens | Source |
|---|---|---|---|
| L1 | **Membrane HP reaches 0 (lysis)** | Necrosis begins. All viruses are neutralised. The membrane disappears. Every organelle takes escalating damage over a 120-frame schedule, and all resources are set to 0. Then a "game over" screen with cause *lysis* appears, and dismissing it restarts the level. | Membrane.as, damage-kill override; Cell.as, necrosis start/tick/end; Engine.as, on-necrosis |
| L2 | **Nucleus HP reaches 0** | A "game over" screen with cause *no nucleus* appears (skipped if lysis is already under way). Retry restarts the level. | Nucleus.as, take-damage; Cell.as, nucleus check |
| L3 | **Scripted failure** | A level objective can end the level as a failure, which restarts it immediately. Only fork slot 6 (shown as Level 7) uses this: the temperature reaching 0 °C. | Engine.as, finish-level ("failure") → Director reset |
| S1 | **"Screwed" warning: no working mitochondrion** | Triggers when every mitochondrion is heavily damaged. The screen offers *Retry* (restart) or *Play on* (dismiss and continue). **Not a forced loss.** | Cell.as, mito screwed check; MenuSystem_Screwed.as |
| S2 | **"Screwed" warning: no working chloroplast** | Same as S1, but only checked while sunlight is above 0. **Added (§K):** checked when a chloroplast drops below 25 HP **or is removed**, so recycling the last one opens it (possibly twice during one recycle). | Cell.as, chloro screwed check |

Things the fork does **not** treat as a loss:
- **Running out of ATP.** Level 0 defines an objective for "ATP at or below 10" that would advance as a consolation, but it is never activated.
- **Virus count.**
- **Timers running out.** Countdowns only schedule waves; none of them fail the level.
- **Nucleus infestation.** It is not a loss by itself. It leads to losses indirectly: an infested nucleus keeps releasing viral RNA, the resulting viruses tear the membrane on their way out, and a torn membrane ends in lysis (L1).
- **Mitosis / daughter cells.** These are *not* extra lives in practice. See §8.3.

Per-action failure codes (FailCode.as) are *not* game states. They are purchase results shown as alerts:
- success
- can't afford (names the missing resources and fires a `not_enough` trigger)
- no room
- no nucleus pore
- no nucleolus pore (**corrected, §I:** this code and "no nucleus pore" show **no alert at all**; §F, D20)
- at max
- already dividing

## 5. Viruses and waves

Sources: Virus.as, VirusInjector.as, VirusInvader.as, VirusInfester.as, EvilRNA.as / EvilDNA.as / RedRNA.as, Nucleus.as, Selectable.as (infestation constants), Cell.as (wave spawning), com/woz/WizardOfOz.as (wave dormancy), Engine.as (wave-kill check), MenuSystem_Reward.as (grades and rewards).

### 5.1 Shared virus behaviour

1. **Approach.** Viruses spawn outside the visible lens and steer toward the nearest membrane segment. They re-aim every 5 frames.
2. **Contact.** On touching the membrane, the defensin shield rolls once (§2.5). If the roll succeeds, the virus dies.
   - A virus wrapped in a **vesicle** is immune to defensins. It loses the vesicle instead.
3. **Clinging.** A virus stuck to the membrane cannot be eaten by lysosomes.
4. **Entry.** Piercing viruses deal **1 membrane damage** at the entry point, unless vesicle-wrapped (infesters always deal it). Inside the cell they move at half speed toward their target.
5. **Replication.** Viral RNA reaches a ribosome and hijacks it. Each finished strand makes the ribosome release a type-specific number of new virions.
6. **Escape.** New virions grow, move to the membrane, and **exit, dealing 1 membrane damage each**. Once outside they fly off-world at 4× speed and join that wave's *dormant pool* (§5.3).
7. **Taunting.** Viruses inside the cell "taunt" every 15 frames, which calls the nearest idle lysosome. Viral RNA taunts too, which calls the nearest slicer.
8. **Viruses are free to spawn.** The code that would charge ATP to spawn a virus (Costs.as, virus make rows) is commented out.

### 5.2 Types

| Type | Speed outside / inside | Entry | Target inside | Payload | Offspring per virion | Main counters |
|---|---|---|---|---|---|---|
| **Injector** | 6 / 3 | Does **not** enter. Lands, injects RNA, and dies. | n/a | 1 viral RNA, which travels from the membrane to a ribosome | **2** | Slicers (cut the RNA); defensins |
| **Invader** | 6 / 3 | Pierces (1 dmg unless vesicle-wrapped) and enters whole | nearest ribosome | 4 viral RNAs released at the ribosome | 4 × 1 = **4** | Lysosomes eat it while it travels inside (one lysosome per virus, not used up); slicers cut its RNA; defensins. **Added (§J):** lysosomes also eat newborn virions of every type, injector offspring included, on their way out. |
| **Infester** | 8 / 3 | Pierces (always 1 dmg) and enters | a **nucleus pore** | 5 viral DNA strands. These are *immune to slicers* and each adds +1 **nucleus infestation** | ongoing (see below) | DNA repair enzymes (clear infestation); lysosomes in transit; defensins |

**Nucleus infestation.** Source: Nucleus.as, infest tick; Selectable.as, infestation constants.
- The scale runs 0–100.
- Every 30 frames the nucleus's spawn rate rises by (infestation ÷ 100) × 5.
- Each whole point of rate releases one viral RNA from the nucleus, and each of those makes **2 infesters** at a ribosome.
  - Example: at 100 infestation, the nucleus releases 5 RNAs/s, which is about 10 infesters/s.
- The growth stops while the wave's live count is above its original size.
- **DNA repair enzymes** heal 5 per enzyme. They remove infestation first and only then restore nucleus HP.

**Ribosome hijack.**
- A viral RNA docks like a normal blueprint.
- If a slicer cuts a strand that is already docked, that ribosome's output is cancelled.
- Hijacked ribosomes are unavailable to the player while busy. This competition is the real cost of an infection.

### 5.3 Wave behaviour

Waves are declared per level. Each has an id, a type, a count, a vesicle fraction and a sleep time, and a level trigger action sends it (§7).

- **Sending a wave.**
  - A danger banner shows the count and type.
  - The camera zooms out if the player was "safe", and battle music starts.
  - Viruses are laid out on concentric rings outside the lens, the first at **1.2 × the lens radius** (added, §H). The first ring holds 7, each further ring doubles, radii step out by 0.2 × the lens radius, and each position is jittered by ±75 px.
  - The first ⌊count × vesicle⌋ viruses arrive vesicle-wrapped.
- **Dormancy and re-attack.**
  - Every escaped or missed virus returns to the wave's dormant pool.
  - The pool's timer ticks every 31 frames (corrected, §H: not once per second), and **resets to 0 on every escape and every virion birth**. When it reaches the wave's sleep time (15 ticks, about 15.5 s after the last escape), the **whole pool attacks again**.
  - Every shipped wave uses a sleep time of 15 s, except level 6, where it is missing, so escapees return after about 1 s.
  - The HUD virus panel shows live and dormant counts, live viral RNA, and a countdown to the re-attack.
- **Defeat.** A wave is defeated only when all of the following hold:
  - its dormant pool is empty;
  - none of its virions or RNA/DNA remain;
  - for infesters, the nucleus is no longer infested by it.

  A wave-defeat screen then shows the grade and reward, and the `enemy_survive` trigger fires.
- **Reward.** Based on the wave's *original* count (MenuSystem_Reward.as):
  - NA = count × payload RNAs × per-type NA reward.
  - AA = count × **virions per RNA** × 2.5. **Corrected (§J):** the code multiplies by the per-RNA spawn count, not offspring per virion. The two agree for injectors (2); for invaders it is 1, so 5 invaders pay 13 AA, not 50.
  - FA = count × virions per RNA × 0.25 × vesicle fraction.
- **Grade.** score = spawned-inside ÷ original + escaped ÷ original + infestation ÷ (original × 5).

  | Score | Grade | GPA |
  |---|---|---|
  | 0 | A+ | 5 |
  | below 1 | A | 4 |
  | below 2 | B | 3 |
  | below 3 | C | 2 |
  | below 5 | D | 1 |
  | 5 or more | D− | 0 |

  - The level's final grade is the wave-size-weighted average of its wave grades.
  - A level with no waves gets 5 (Engine.as, final GPA).
- **No random virus encounters.** The ambient "random virus encounter" code exists but is disconnected (WizardOfOz.as, virus encounter tick). Viruses only arrive in scripted waves.

## 6. Environmental meters and hazards

| Meter | Range / display | What moves it | What it affects | Player counter | Source |
|---|---|---|---|---|---|
| **Sunlight** | 0–1, shown as % | Level actions only (values used: 1, 0.5, 0.2, 0.1) | Chloroplast glucose rate (counting period = 30 frames ÷ sunlight, plus the process animation, §1.3, §K; 0 = stops). At 0, the "no chloroplast" warning is skipped. | none directly: toggle or divide chloroplasts | Sunlight.as; Cell.as, set sunlight; Chloroplast.as |
| **Free radicals** | count of particles | Mitochondria (0.5 per production cycle) and chloroplasts (1 per cycle), only while the level has radicals switched on. The first cycle never spawns one. Levels can also spawn radicals on command. | Each hit does **5 damage** to one organelle. Target weights: mitochondrion 10, chloroplast 10, lysosome 2, peroxisome 1, slicer 0.5 (each × count), plus a nucleus share of 1/3 of that total. **Added (§I):** the total is multiplied by 1.05, leaving a "no target" band; a radical that rolls no target, or its own maker, drifts away and then homes back onto its **maker** and damages it. So the nucleus always gets 1 ÷ 4.2 ≈ 23.8%, and with one mitochondrion and nothing else about 76% hit the mitochondrion. Radicals never expire. Speed 2.5 px per frame, easing by 1/30 per frame to 2. They never target the membrane, the ER or ribosomes. The lysosome band omits the peroxisome weight (§F, D21): **corrected (§J)**, its width is 2 × lysosomes − peroxisomes, so it is empty when peroxisomes ≥ 2 × lysosomes, and the share it loses falls into the "no target" band, which sends the radical back to its maker. | Peroxisomes (each absorbs up to about 20); turning producers off; auto-replace peroxisomes | ProducerObject.as, radical check; FreeRadical.as; Peroxisome.as |
| **Invincible radicals** | n/a | Chance 0 for both producers, so **never spawned** in shipped play | Would be immune to peroxisomes | n/a | Chloroplast.as, ProducerObject.as |
| **Temperature** | 0–25 °C (display clamps). Crossing 0 fires `temperature_min`; crossing 25 fires `temperature_max`; each whole degree fires hot/cold. | Level drift (fork slot 6, shown as Level 7: starts at 8 °C and falls 1 °C per 450 frames ≈ 15 s); the mitochondrion's **"brown fat"** toggle (+0.25 °C per burn instead of ATP) | **No damage or rate effect.** It exists only as an objective and fail trigger. | brown-fat toggle | ToxinLevel.as (temperature mode); Engine.as, heat change / temperature check; ProducerObject.as |
| **Toxin level** | 0–100, shown as % | Golgi "make toxin" action: each packet adds **+2** once it has left the cell | Nothing harmful. It is the **win counter of fork slot 7 (shown as Level 8)**. | n/a (it is a goal) | Cell.as, make toxin; ToxinParticle.as; ToxinLevel.as |
| **Cytosol pH** | starts 7.5; lysosome interior 4.5 | Only lysosome acid release ("pop"), which is **not exposed** to the player. So in shipped play the cytosol stays at 7.5. | Below 7.5 − 1.0: acid damage per second = ((7.5 − pH − 1) ÷ 6.5) × 15, a hard kill. A "reduced efficiency" band between 0.25 and 1.0 below 7.5 is commented but not implemented. Acidified digestion vesicles (×3 damage) are how recycling works. | lysosome count | PH.as; CellObject.as, pH damage; Lysosome.as; BigVesicle.as |

## 7. Level trigger vocabulary

Sources: com/pecLevel/BakedLevel.as (parser), ObjectiveHandler.as (condition evaluation), ObjectiveType.as / ObjectiveCondition.as (constants), Engine.as (objective-action dispatch and trigger emitters), Cell.as and Interface.as (further emitters).

### 7.1 Objective model

A level is a flat list of **objectives**.

**Fields.** Each objective has:
- an id;
- whether it is active at load;
- a *trigger* flag:
  - true: its tutorial and actions fire immediately on completion;
  - false: the player must press a "Next" button first;
- a chime level (0–2);
- a **condition** made of:
  - a type;
  - a target type;
  - a target number;
  - a comparison: true / false / eq / gr / ls / gr_eq / ls_eq;
  - an optional delay in frames;
  - a *hidden* flag, which keeps it off the objective list;
- **pre-actions**, which run as the objective's tutorial begins;
- a **tutorial** sequence of slides;
- **actions**, which run after the tutorial closes, or at once if there is none.

**Chaining.**
- Objectives chain by activating one another.
- A "join" (wait for N parallel goals) is built from several hidden objectives that each raise a named flag, plus one objective that waits for the flag count to reach N.
- When a newly activated objective's condition is already true, it completes immediately. This covers "have X" checks.

**Progress rules.**
- *Have*-style conditions track the current value.
- *Do*-style conditions accumulate from the moment of activation.

### 7.2 Condition types

| Type | Meaning | Target examples | Used in levels? |
|---|---|---|---|
| `game_load` | Level finished loading (used to set up the HUD and environment) | — | all |
| `game_start` | Intro animation done; opening briefing | — | all |
| `have_resource` | Current amount of a resource meets the comparison | atp, na, aa, fa, g | 0, 1, 2, 4 |
| `have_thing` | Current count of a unit type meets the comparison | mitochondrion, ribosome, slicer, lysosome, peroxisome, chloroplast, radical, transport vesicle, defensin, toxin | 0–4, 7 |
| `get_thing` / `lose_thing` | Count created / destroyed since activation | — | no (buggy; §F) |
| `do_action` | Player performed an action N times since activation | buy_ribosome, buy_slicer, buy_lysosome, make_membrane, make_defensin, move, prepare_move, enable, disable, enable_auto_peroxisome, pseudopod_finish | 0–4 |
| `fail_action` | Player tried an action and it failed | — | no |
| `select_thing` | Player selected a unit type | nucleus, slicer, er, golgi, centrosome | 1, 2 |
| `click_newthing` | Player accepted a "new organelle" pop-up | nucleus, centrosome, er, golgi | 0–3 |
| `halfplop_organelle` | The new organelle has half-dropped into the cell | same | 0–3 |
| `plop_organelle` | Full drop | — | no |
| `show_action_cost` | Player hovered an action's cost panel | buy_ribosome, buy_slicer | 1 |
| `expand_membrane` / `shrink_membrane` | Membrane node added / removed | — | 2 |
| `radical_damage` | A radical hit the target | nucleus | 2 |
| `absorb_radical` | A peroxisome absorbed a radical | — | 2, 4 |
| `heal_thing` | The target was healed (by DNA repair) | nucleus | 2 |
| `not_enough` | A purchase failed for lack of this resource | fa | 4 |
| `throw_flag` | A named flag has been raised N times (join primitive) | named flags | 1, 3, 4, 5 |
| `enemy_alert_finish` / `enemy_alert_start` | Finish: the wave's HUD virus-panel glow (about 60 frames) ended, once per new wave line, not on re-attacks (corrected, §H: the banner call is commented out). Start: the warning started | wave id | 1, 3 (finish only) |
| `enemy_survive` | A named wave is fully defeated, or `all` | wave id | 1–6 |
| `enemy_alert` | Declared, but its event is never sent | — | no (dead) |
| `virus_dormant` | Some escaped viruses are waiting to re-attack | wave id | 1 |
| `countdown` | The level countdown timer reached 0 | — | 5, 6, 7 |
| `temperature_change` | Temperature crossed a whole degree, hot or cold | hot / cold | 6 |
| `temperature_min` / `temperature_max` | Temperature hit 0 / 25 | — | 6 (min only) |
| `survive_time` | Declared, never sent | — | no (dead) |
| `virus_entry_wound`, `virus_exit_wound`, `virus_escape`, `toxin_escape` | Emitted by the engine but never listened for | — | no |

Comparison `true` completes on any nonzero progress; `false` completes on zero.

### 7.3 Action types

| Action | Parameters | What it does |
|---|---|---|
| `activate_objective` / `deactivate_objective` | id | Turn objectives on or off (chaining) |
| `showTutorial` | heading + slide list | Modal tutorial. Slides tagged as talk / discovery / new-action get portraits or sounds. |
| `discovery` | id | Unlocks an encyclopedia entry and bumps the level's discovery counter. The level's maximum is the number of discovery actions it contains. |
| `show_resource` / `hide_resource` | resource id | Show or hide a resource meter |
| `show_interface` / `hide_interface` | HUD element id | HUD elements: quant panel, discovery score, pH, daughter cells, sunlight, toxin level, temperature, menu, pause, tutorial panel, membrane health, zoom slider |
| `hide_organelle` / `show_organelle` | nucleus, centrosome, er, golgi | Hide a built-in organelle (the reveal is done with `plop_organelle`) |
| `plop_organelle` | id | Drop a hidden organelle into the cell with an animation |
| `show_newthing` | organelle id | Pop the "new organelle available" card |
| `wipe_organelle_act` / `add_organelle_act` | organelle, action | Clear or append that organelle's action menu. **This is the unlock mechanism.** |
| `spawn_object` | id, location mode, move mode, count, value, distance | Place resource gems (`gem_<res>` worth *value*) or floating vesicles carrying an organelle (optionally damaged). Location: in a ring around the lens, or at a random nearby point; optionally drifting toward the cell. |
| `activate_stuff` | batch id | Switch on an ambient resource spawner (§1.2) |
| `set_cyto_process` / `set_fat_process` | true/false | Cytoplasmic glycolysis on/off / surplus-glucose-to-fat on/off (the fat switch is never used by any level) |
| `set_radicals` / `spawn_radicals` | on/off; count, origin, target, delay | Radical production on/off; force radicals from an organelle at a target |
| `set_sunlight`, `set_toxinlevel`, `set_temperature` | value | Set an environment meter |
| `set_heat_change` / `stop_heat_change` | amount, frames | Start or stop a steady temperature drift |
| `send_wave` | wave id | Launch a declared wave (§5.3) |
| `throw_flag` | name | Increment a named counter (for joins) |
| `start_countdown` | seconds | Start or restart the on-screen timer; it fires `countdown` at 0 |
| `set_resource` | resource, value | Hard-set a resource amount |
| `set_zoom`, `set_scroll_to` | value/target, time | Camera moves during a brief "faux pause" |
| `set_arrow_show` | — | Show the tutorial pointer arrow |
| `speech` | talker, emotion, text | Speech bubble (unused) |
| `finish_level` | victory / consolation / failure | Victory and consolation both show the grade screen and advance. Failure restarts the level. |

### 7.4 Other level-file sections

- **Level info.**
  - Title (ignored; the level picker uses built-in names).
  - World size: the boundary is always a circle of radius width ÷ 2. The box option is ignored.
  - Start position.
  - Background skin: petri dish, green, gold or silver dish, or monster mouth.
  - Starting resources.
  - Starting counts of mitochondria, chloroplasts, ribosomes, lysosomes, peroxisomes and slicers.
  - A membrane-node field that is **never read**.
  - The nucleus, ER, Golgi and centrosome always exist. A level hides them and later "plops" them in.
- **Level stuff.** Ambient spawners. Only the resource "goodies" batches work (name, type, pool, spawn chance, active). Enemy and object spawners are parsed but inert.
- **Level things.** Placed objects are parsed but ignored. **Waves** are declared here: id, type, count, vesicle fraction, sleep time, active. Spread, delay and release count are parsed but unused.

## 8. Level list

Slots 0–7 are shown to players as levels 1–8 (GameLevelInfo.as, level labels and names). The **display names** come from that file. Several XML titles differ and are ignored.

For every level:
- **Win:** a scripted "victory", which leads to the grade screen and then the next slot.
- **Loss:** the global states in §4 (lysis, dead nucleus, and optional restarts from the "screwed" warning).
- Level-specific losses are listed where they exist.

| Slot / shown as | Name | Start [ATP, NA, AA, FA, G] | Start units | Introduces (what the beats teach) | Waves actually sent | Win | Extra loss |
|---|---|---|---|---|---|---|---|
| 0 / 1 | Pseudopod for the Win | [1000, 0, 0, 0, 0] | none; nucleus, ER, Golgi, centrosome hidden | Claim the centrosome; the cytoskeleton and membrane; pseudopod movement (3 times); ATP pays for movement; HUD tour; glucose becomes ATP in the cytoplasm; a mitochondrion floats in and makes ATP efficiently | none | Reach ≥ 1000 ATP (the level starts at 1000, so this means earning back what movement cost) | none (a "low ATP" objective exists but is never activated) |
| 1 / 2 | Let's Get Nuclear | [500, 0, 0, 0, 25] | 1 mitochondrion | Claim the nucleus; amino acids and nucleotides (gems spawned); selecting and the build menu; buying ribosomes (reach 20); camera zoom and scroll; cost preview; slicers (reach 10); temporary RNA costs; membrane health; escaped viruses return | injector 5, then injector 15 | Defeat both waves | — |
| 2 / 3 | Insane in the Membrane | [1000, 10, 100, 0, 50] | 1 mitochondrion, 25 ribosomes, 50 slicers | Group-select and recycle; surplus glucose turns into fatty acids (reach 10 FA); claim the ER; make membrane (expand 3 times, then shrink once); free radicals damage the nucleus; moving and switching off a mitochondrion; DNA repair; peroxisomes (reach 3) absorbing radicals | injector 15, then injector 25 (a declared 30-injector wave is never sent) | Defeat both waves | — |
| 3 / 4 | Invasive Infection | [500, 10, 500, 50, 50] | 1 mitochondrion, 25 ribosomes, 5 peroxisomes; radicals on | A damaged mitochondrion floats in; claim the Golgi; lysosomes (reach 10); recycling a damaged mitochondrion; dividing a mitochondrion; invader viruses eaten by lysosomes | invader 5; then invader 10 + injector 15 together | Defeat both final waves (flag join) | — |
| 4 / 5 | Green Thumb | [1000, 10, 550, 30, 50] | 1 mitochondrion, 30 ribosomes (above the cap of 25), 10 lysosomes, 3 peroxisomes; sunlight 1; radicals on | A chloroplast floats in; sunlight → glucose, plus more radicals; absorb 5 radicals; dividing a chloroplast (tip on any FA shortfall, D26); absorb 10 more; auto-replace peroxisomes; build fat (FA ≥ 10; tip on the first ATP change ≤ 500); defensins (reach 5 fused) | invader 10; then invader 15 (**7** vesicle-wrapped, §K) | Defeat both waves | — |
| 5 / 6 | The Longest Journey | [2500 → **hard-set to 1000** at start, 25, 400, 50, 50] | 1 mitochondrion, 1 chloroplast, 25 ribosomes, 2 lysosomes, 2 peroxisomes | Survival gauntlet with no glucose feed; sunlight dips to 0.2 and then recovers; introduces **infesters** | countdown 60 s → injector 20 → 20 s → invader 15 → infester 5 → 20 s → {inj 15, inv 10, inf 5} → 20 s → {inj 30, inv 25, inf 10; 10% vesicles} → 20 s → {inj 20, inv 25, inf 20; 20% vesicles} | Defeat the last trio (flag join) | — |
| 6 / 7 | Heat Shock Crisis | [1500, 35, 1000, 100, 50] | 1 mitochondrion, 1 chloroplast, 30 ribosomes, 10 lysosomes, 3 peroxisomes; sunlight 0.5; fixed glucose pool, no regrowth | **Cold**: starts at 8 °C and cools about 1 °C per 15 s. Unlocks the mitochondrion's brown-fat heating. | after reaching 10 °C: 30 s → injector 15 → (defeat) 60 s → invader 15 → 60 s → infester 15 → 60 s → an undefined wave (§F) | Temperature reaches **25 °C** | **Temperature hits 0 °C → level failure (restart)** |
| 7 / 8 | Indigestion | [1500, 50, 1250, 25, 10] | 2 mitochondria, 2 chloroplasts, 30 ribosomes, 1 peroxisome; sunlight 0.1; "monster mouth" background | Unlocks the Golgi "make toxin" action: fill the toxin meter while under timed attack | Toxin thresholds 10, 25, 50, 75 and 90 each send a trio or single wave (injectors 30, invaders 17, infesters 15; 50–100% vesicle-wrapped). A parallel timer chain sends escalating waves every 90–120 s (10 → 60 per type). | Toxin level reaches **100** (50 packets) | none scripted |

### 8.1 Unlock order of actions (fork)

| Slot | Actions unlocked |
|---|---|
| 1 | Nucleus: buy ribosome, buy slicer. **The mitochondrion already has its full default menu** (move, toggle, recycle, divide), because slot 1 never removes it (added, §H). |
| 2 | ER: make membrane, take membrane, buy peroxisome. Mitochondrion: move, toggle. Nucleus: buy DNA repair. |
| 3 | **Corrected (§J):** at load, the menus are rebuilt: nucleus buy ribosome, slicer and DNA repair; ER make and take membrane, buy peroxisome (no auto-peroxisome); mitochondrion move and toggle; **peroxisome move**; the Golgi empty and hidden. Then Golgi buy lysosome after the Golgi arrives; mitochondrion and peroxisome recycle after the 10-lysosome objective; mitochondrion divide after the recycle objective. Lysosomes keep the Engine's move and recycle. |
| 4 | **Corrected (§K):** at load, the chloroplast menu is move, toggle and recycle; the Golgi's is buy lysosome only; the ER's is make and take membrane and buy peroxisome. Then chloroplast divide after the 5-radical objective; the ER's auto-peroxisome toggle after the 10-radical objective; Golgi make defensin after the 10-FA objective; Golgi take (recycle) defensin after the first wave is defeated. The mitochondrion keeps its full default menu. |
| 5 | No menu changes; every organelle keeps its full default action list |
| 6 | Mitochondrion: brown fat |
| 7 | Golgi: make toxin |

Organelles "arrive" in this order:
1. centrosome (slot 0)
2. mitochondrion (slot 0, floats in)
3. nucleus (slot 1)
4. ER (slot 2)
5. Golgi (slot 3)
6. chloroplast (slot 4, floats in)

### 8.2 Cutscenes

- **What plays.** In this fork, slots 0, 1, 4, 5 and 6 all play the same generic placeholder scene. Slot 7 plays the finale and then the credits. Slots 2 and 3 have none.
- **What was originally planned.** A commented-out table shows the intended sequence:
  - lab intro after 0
  - lab board after 1
  - launch after 4
  - crash after 5
  - landing and the crocodile after 6
  - finale after 7

  (GameLevelInfo.as, cinema table.)
- **Content.** Scene content lives in the `.fla` and was not read.
- **After a scene.** The game returns to the level picker.

### 8.3 Mitosis and "daughter cells" (confirmed)

- A mitosis action exists. It plays a cutscene and then increments an extra-lives counter, which the HUD shows as "daughter cells" (Engine.as, execute-mitosis and finish-mitosis; Director.as, cinema finish).
- **The counter is never consumed.** No loss path checks it.
- The mitosis action is commented out of the nucleus's default menu, and no level adds it.
- **Conclusion:** there are no extra lives in the Baseline.

### 8.4 Persistence

- Progress is saved locally: levels beaten, best time and grade per level, and whether the game has been beaten (LevelProgress.as, local shared object).
- Levels unlock in sequence.

### 8.5 Slot 0 (shown as Level 1) specifics

Source: level_00.xml (level info, level stuff and objectives), together with the routines cited.

- **World.**
  - Size 500 × 500, so the boundary is a circle of radius 5,000 px.
  - Start (0, 0), petri-dish background.
  - Membrane 15 nodes, so the lens is ≈ 1,191 px. Zoom 0.2, and the zoom control is hidden.
- **Goodie pools declared (all start inactive):**

  | Pool | Count | Spawn chance per regrowth tick | Active at load | Ever activated in slot 0? |
  |---|---|---|---|---|
  | G | 200 | 0.3 | no | **yes**, by the third-pseudopod objective |
  | NA | 50 | 0.05 | no | no |
  | FA | 50 | 0.1 | no | no |
  | AA | 50 | 0.1 | no | no |

  - Once active, the G pool starts at a 17.6% chance per spawn roll (§1.2). It regrows by about 0.56 gems per second (0.3 per 16 frames).
- **Beat order (mechanics only).**
  1. Claim the centrosome.
  2. Complete 3 pseudopods. Each is a `pseudopod_finish` objective, and cancelled pseudopods count too (§2.4).
  3. The third one does three things: it activates the G pool, spawns 18 glucose gems and arms "find glucose".
  4. Having at least 1 G switches on cytoplasm glycolysis and spawns the mitochondrion vesicle.
  5. Having at least 1 mitochondrion arms the ATP ≥ 1000 win.
- **Scripted glucose ring** (WorldCanvas.as, spawn-object and location-from-id):
  - 18 gems worth **5 G** each, in "surround" mode with distance 1.
  - **Ring radius** = (1.05 + 0.25 × distance) × lens = **1.30 × lens ≈ 1,549 px** from the centrosome. That is outside the visible lens.
  - **Angles.** Gem 0 sits at 0°. Gem *i* sits at *i* ÷ 17 of a full turn, so the last gem lands on top of the first: **17 visible spots, with 2 gems stacked at 0°**.
  - The gems are static. Any gem that ends up more than 2 × lens from the cell goes into the G pool as +1 (§1.2).
- **Scripted mitochondrion** (WorldCanvas.as, spawn-object, location-from-id and move-spawn-object; CanvasWrapperObject.as; GameObject.as, move-to-object):
  - **Spawn.** One "vesicle wrapper" carrying a mitochondrion, in "nearby" mode (random angle) with no distance parameter. That puts it at **1.05 × lens ≈ 1,251 px** from the centrosome.
  - **Movement.** Move mode "towards cell": it homes on the centrosome in float mode.
  - **Speed.** It starts at 2 px per frame, and its heading and speed are recomputed every 15 frames. The speed becomes 10 px per frame while it is farther than √1.1 × lens (≈ 1.049 × lens) from the centrosome, and **1 px per frame** once it is closer.
    - Because it spawns just outside that threshold, in practice it crawls in at about 1 px per frame unless the cell moves toward it.
  - **Size.** The wrapper's radius is 1.25 × half the larger dimension of its icon graphic. The value lives in the `.fla` art and was not read.
- **Engulfment of the mitochondrion** (Membrane.as, collision test and merge-vesicle; Cell.as, make-vesicle-content, place-vesicle-mito and place-mitochondrion; HardPoint.as, escort):
  - **Detection.** The same segment-vs-circle touch test as gems, not an inside test. It fires the moment any membrane segment overlaps the wrapper's circle.
  - **Effect.**
    - Any extending pseudopod is cancelled.
    - The wrapper is removed.
    - A mitochondrion is created at the wrapper centre, moved 3 × wrapper radius toward the centrosome.
    - It gets a microtubule and floats to a resting spot 0.75 × the centre radius (≈ 0.525 × membrane radius ≈ 208 px) from the centrosome, in the same direction.
    - While it floats, an escort hard point of twice its radius travels with it and pushes the membrane out.
  - The mitochondrion counts toward "have mitochondrion" from the moment it is created.

## A. Conflicts: fork vs AC doc

Level numbers below use the AC doc's 1-based numbering, which equals fork slot + 1. "Fork wins" is the default for mechanics and numbers under the Baseline definition. Each row is still a decision for later tickets.

| # | Topic | Fork | AC doc | Notes |
|---|---|---|---|---|
| C1 | Pseudopod cost | 10 ATP × (distance ÷ 500 px)², quadratic, no fixed stroke cost | "60 ATP per stroke" example, scaling with drag (Pass 1 §4.2) | The AC also says strain makes pseudopods cost up to +50% (Pass 3 §3.1) and double below 15 °C (Pass 6). Neither exists in the fork. |
| C2 | Membrane "make" cost and effect | [100, 1, 50, 5, 0]; +1 node, +10 max HP, capped at 25 nodes | 10 FA + 20 ATP; either "+25 HP" or "bigger perimeter" (Pass 3 §3.2) | |
| C3 | Membrane repair | 1 HP per 25 ATP, every 2 s, automatic | 25 ATP per HP (Pass 2 §5.3) ✔; but "per puncture tick" (Pass 3) and "up to 60 ATP/s" (Pass 4) | Pass 2 agrees with the fork; the later passes contradict it. |
| C4 | Membrane tension mechanics | Stretch only decides where nodes are added or removed; no gameplay penalty | Past a tension threshold, pseudopods slow down and punctures do double damage (Pass 3 §3.1) | |
| C5 | Where things are bought | Nucleus: ribosome, slicer, DNA repair. ER: membrane, peroxisome. Golgi: lysosome, defensin, toxin. | Nucleus: ribosomes, slicers, ER, lysosome, peroxisome, heat shock proteins (Pass 2–6). Golgi: defensin, toxin. | The AC also routes ribosome and peroxisome builds through the centrosome. |
| C6 | Ribosome cost | [2, 1, 10, 0, 0] each, bought 5 at a time (so 10 ATP, 5 NA, 50 AA per batch); cap 25 | 25 ATP, 5 NA (temporary), 50 AA per 5 (Pass 3/4 tables) | NA and AA agree; ATP differs (10 vs 25). In the fork the ribosome's NA is permanent (no RNA is involved), not temporary. |
| C7 | Slicer cost | [5, 1, 10, 0, 0] each, ×5 per click (so 25 ATP, 5 NA, 50 AA per batch); cap 50 | 25 ATP, 5 NA (temporary), 50 AA per 5 (Pass 2 §5.1) | **Agrees** ✔. |
| C8 | Lysosome cost and health | [15, 1, 25, 1, 0], bought 2 per click at the Golgi; HP 3 | 75 ATP, 15 NA, 120 AA; HP 200; acid leak of 5 HP/s (Pass 4) | The AC's acid leak numbers do not exist in the fork. A lysosome "pop" that releases acid into the cytoplasm *is* implemented but never offered to the player (see §G). |
| C9 | Peroxisome | [50, 1, 50, 2, 0] at the ER; HP 20; worn down 1 per radical | 60 ATP, 10 NA, 80 AA; HP 150 (Pass 5 §1.2) | |
| C10 | ER / Golgi / nucleus / centrosome | Always present; revealed ("plopped") in by script; never bought | "Buy 1 ER" at the nucleus for 50 ATP, 10 NA, 100 AA (Pass 3 §2.2); Golgi build cost not given | |
| C11 | Defensin | [100, 1, 50, 1, 0]; strength = defensins ÷ nodes, capped at 50% | 40 ATP, 40 AA, 10 FA; 50% cap ✔ (Pass 5 §3.1) | The cap agrees. The AC's "shrinking raises defensin density" also holds in the fork (fewer nodes → higher ratio). |
| C12 | Mitochondrion division | [250, 5, 100, 25, 10]; cap 10 | 100 ATP, 20 NA, 50 AA, 25 FA (Pass 6 §1.2) | The AC places division in level 6; the fork unlocks it in slot 3 (AC level 4). |
| C13 | Chloroplast division | [200, 5, 100, 25, 10] | 120 ATP, 25 NA, 60 AA, 30 FA; "doubles output" | |
| C14 | Organelle HP | Nucleus 200 ✔, ER 100, Golgi 250 ✔, lysosome 3, peroxisome 20, chloroplast 100, mitochondrion 100, centrosome 250 | ER 250, lysosome 200, peroxisome 150, chloroplast 300 | |
| C15 | Radical hit damage | 5 per hit | 2–5 per hit (Pass 5 §2.2) | Compatible range. |
| C16 | Lose conditions | Lysis; dead nucleus; the level 7 freeze; "screwed" warnings (§4) | Only membrane 0 → lysis (Pass 2 §5.3). Win thresholds in the AC ("≥ 75% health", "zero organelles lost") have no fork equivalent. | |
| C17 | Level 3 waves (fork slot 2) | injector 15, then 25 | 8, then 20 | |
| C18 | Level 4 (slot 3) content | Introduces Golgi + lysosomes + mitochondrion recycle/divide; waves invader 5, then invader 10 + injector 15 | Lysosomes + "enveloped" viruses + Viral Protease; waves 6 env + 10 inj, then 14 + 25 | "Viral Protease" does not exist in the fork. |
| C19 | Invader behaviour | Enters whole, goes to a ribosome, releases 4 RNAs. Lysosomes eat it in transit; slicers cut its RNA. | "Enveloped": slicers can't harm it, a lysosome must strip its coat (Pass 4 §2.1) | The fork's *vesicle-wrapped* viruses (immune to defensins) are the nearest analogue to "enveloped". |
| C20 | Level 5 (slot 4) content | Chloroplast, auto-peroxisome, defensins. The **Golgi and peroxisomes were introduced earlier** (slots 2–3). | Chloroplast, peroxisome, Golgi, defensins (Pass 5) | The AC also lists the peroxisome in its level 4 nucleus menu. |
| C21 | Level 5 waves | invader 10; then invader 15 (50% wrapped) | 15 fast injectors; then 12 enveloped + 20 injectors | "Fast injector" does not exist in the fork. |
| C22 | Level 6 "The Longest Journey" | Glucose-starved survival gauntlet with sunlight dips; **infesters introduced**; no temperature | Deep-space cold, temperature, fission, shrink-to-concentrate-defensins, cold-adapted infesters that freeze ribosomes | The infester in the fork is a **nucleus-infesting** virus countered by DNA repair. |
| C23 | Level 7 "Heat Shock Crisis" | Despite the name, a **cold** level: start 8 °C, cooling; warm up to 25 °C with brown fat to win; 0 °C = fail. No heat shock proteins. | Overheating to 45–55 °C; heat shock proteins; 6–8 peroxisomes; thermophilic mutants | These are opposite mechanics. Heat shock proteins do not exist in the fork. |
| C24 | Temperature model | 0–25 °C meter, objective and fail trigger only, no effects | Normal 25–37 °C; below 15 °C diffusion drops 50–80% and pseudopods cost double; above 42 °C denaturation dissolves slicers | |
| C25 | Level 8 "Indigestion" | Toxin meter to 100 (+2 per packet at [50, 1, 25, 1, 0]); waves at toxin thresholds plus a timer chain; pH never changes | Toxin packets at 75 ATP, 15 NA, 120 AA, 20 FA; a waves-by-toxin gauntlet of 6 waves; stomach pH 1.5–3.0 erodes the membrane | The structure (toxin thresholds → waves → 100% wins) **agrees**. Counts, costs and pH do not. |
| C26 | Glucose HUD counter | Glucose held by the cell | Pass 2 §1.1 describes it as glucose nodes remaining *outside* the cell | |
| C27 | Level 2 (slot 1) | 20 ribosomes, 10 slicers, waves of 5 and 15 | Same ✔ (Pass 2 §6.2) | Listed as a consistency check. Pass 1–2 facts generally match the fork. |
| C28 | Level 1 (slot 0) | Starts with 1000 ATP; win at ≥ 1000; no mitochondrion at start (one floats in mid-level); the centrosome is claimed first | Goal 1000 ATP ✔; mockup shows 1 mitochondrion in inventory | |
| C29 | Level names | Picker: Pseudopod for the Win, Let's get Nuclear, Insane in the Membrane, Invasive Infection, Green Thumb, The Longest Journey, Heat Shock Crisis, Indigestion | Same 8 names, but level 5 is also called "Toxic Metabolism" and "Down to E-4R1H", and level 6 "Cold Void" | |
| C30 | Things only in the AC | — | Heat shock proteins, Viral Protease, lysosome acid leak or self-destruct, ER "Expand Storage", a lipid-overload alert, the glucose rate formula, centrosome upgrades, antibodies, plant cell wall, thylakoid mechanics | None of these are in the fork. The Baseline has no numbers for them. |
| C31 | Things only in the fork | DNA repair enzyme; nucleus infestation; free-radical targeting weights; brown-fat heating; wave dormancy and re-attack; the virus-count wave grade formula; resource gems that spawn only while moving; recycling via lysosome digestion; auto-peroxisome | Absent, or only hinted at (e.g. "escaped" count on the wave modal) | |

## B. Gaps (neither source covers)

- **The actual frame rate.** The fork's timings are in frames; 30 fps is inferred from comments. The `.fla` holds the true value.
- **Cutscene and story content.** The fork ships generic placeholders, and the real scene content is in the `.fla`. The AC's story beats are uncited (Passes 3–7).
- **Encyclopedia text and unlock rules** beyond "a discovery action unlocks an entry" (AC Pass 8 is unchecked).
- **Tutorial slide art and layout.** It lives in the `.fla`.
- **The pH mechanic in play.** The fork implements acid damage, but the player can never lower the cytosol pH, and the "reduced efficiency" band is unimplemented. The AC's stomach-pH numbers are uncited.
- **Real consequences of temperature.** Neither source's temperature effects are grounded: the fork has none, and the AC's are uncited.
- **What damaged organelles look like and how they recover** beyond "stops producing". Neither source defines a repair action other than DNA repair for the nucleus.
- **Balance intent.** No source explains *why* costs are what they are. For example, the fork's pseudopod cost is quadratic while the AC's is linear.
- **Save schema, audio, pause/menu contents.** The AC names them without specifying them; the fork uses a local shared object with levels beaten, time and grade.
- **Level 7 (slot 6) after the third timed wave.** The script calls a wave and an objective that are not defined (see §F), so intended behaviour past that point is unknown.

## F. Fork defects — not Baseline intent

These are bugs or dead code in the fork. Do not treat them as intended behaviour. Each one needs an explicit decision, not silent replication.

| # | Defect | Effect | Source |
|---|---|---|---|
| D1 | **Dead resource caps.** The clamp caps only ATP (10,000). The NA/AA/FA/G branches assign each value to itself. The volume-based cap is commented out. | NA, AA and FA are effectively unbounded. Glucose is limited only by the surplus-to-fat or burn tick. | Engine.as, max-resource clamp / update-max-resources |
| D2 | **Unused refund constant.** A 75% "entropy" refund is declared but never used. | Recycling refunds 100% of NA/AA/FA/G, less the ATP charge. | Costs.as |
| D3 | **Shrink before paying.** "Take membrane" removes the node before checking the 50 ATP fee. | If the player can't pay, the membrane shrinks with no refund. | Cell.as, sell membrane |
| D4 | **Ribosome batch uses the slicer's multiplier.** | No visible effect today, because both are 5. | Engine.as, buy-basic-unit dispatch |
| D5 | **DNA repair cap never checked.** | DNA repair enzymes are unlimited. | Engine.as, buy DNA repair |
| D6 | **`lose_thing` precedence and sign bug.** It skips the completion guard and adds +1 on destruction. | The condition is unusable (no level uses it). | ObjectiveHandler.as, thing update |
| D7 | **`enemy_alert` and `survive_time` are never emitted.** | Dead condition types. | ObjectiveHandler.as / Engine.as |
| D8 | **Undefined ids in slot 6.** The last countdown sends an undeclared wave and activates an undeclared objective. The activation throws. | This is a latent halt at the end of the timed chain. The level can still be won through temperature. | level_06.xml, final timer objective; Engine.as, activate objective |
| D9 | **Slot 6 waves lack a sleep time**, so it defaults to 0. | Escaped viruses re-attack after about 1 s instead of 15. This may be unintended. | level_06.xml; WizardOfOz.as |
| D10 | **Ignored level fields:** membrane node count, box boundary, XML title/index, wave spread/delay/release count. | The data implies features that do not exist. | BakedLevel.as |
| D11 | **Instant checks miscount.** The instant "have thing" check misspells lysosome and cannot count toxin, defensin, vesicle or radical. | Those objectives advance only on live events. | Cell.as, count thing |
| D12 | **Extra lives never consumed.** Mitosis increments a counter nothing reads. | No gameplay effect (§8.3). | Engine.as |
| D13 | **Level 0 ATP goal.** The level starts at 1000 ATP and its win check is ≥ 1000. The check only fires on a resource change, so it passes the moment ATP returns to 1000. | This is probably intentional ("earn back what you spent"), but it is fragile. | level_00.xml, final objective |
| D14 | **Level XML is not strictly well-formed.** It contains an unescaped ampersand in a tutorial heading. | The level files need lenient parsing. | level_00.xml |
| D15 | **Pool debited for a gem that never appears.** A successful spawn roll takes 1 from the pool first. The gem is then not made if its spot falls outside the world boundary. | Near the world edge, pools drain silently. | WizardOfOz.as, encounter-goodie; WorldCanvas.as, encounter-goodie-gem |
| D16 | **Off-by-one tick counters.** The pool-regrowth and spawn-roll counters fire only after *exceeding* 15 and 5, so the real periods are 16 frames and 6 movement ticks. | Slightly slower than the constants suggest. | WizardOfOz.as, tick-time / tick-space |
| D17 | **Scripted ring stacks two gems.** The Level 1 glucose ring places gem *i* at *i* ÷ (count − 1) of a turn, so the first and last gems land on the same spot: 17 visible positions for 18 gems. | One spot silently holds two gems. | WorldCanvas.as, location-from-id (surround mode) |
| D18 | **Partial batches.** A multi-unit purchase buys units one at a time until one fails, keeps them, then shows a failure alert and reports the action as failed, so a `do_action` Objective doesn't count it. | The student can buy 3 of 5 under a "can't afford" alert, and the purchase isn't counted. | Engine.as, try-buy-basic-unit |
| D19 | **Nucleolus pores are never returned.** A ribosome takes a pore when it is generated without the pore's open animation, so the pore never goes back to the ready list. A purchase needs more than 1 ready pore at each unit's check; a ribosome generated with none ready uses a busy pore's spot. | Orders after partial fills can hit "no nucleolus pore" below the nominal cap. **Corrected (§I):** "rebuying after recycling ribosomes" can't happen, because players can never recycle ribosomes. There are **24 pores**; a pore goes back to the ready list only from the last frame of its open animation, which nothing plays for nucleolus pores, and then it is the oldest busy pore that returns. | PoreMatrix.as, free-pore, get-pore and animate-finish; Cell.as, generate-ribosome |
| D20 | **Silent pore refusals.** The alert switch falls through to an empty message for "no nucleolus pore" and "no nucleus pore". | The purchase just stops, with no explanation. | Engine.as, show-immediate-alert-code |
| D21 | **Radical lysosome band omits the peroxisome weight.** The cumulative threshold for lysosomes is built without the peroxisome term. | **Corrected (§J):** the lysosome band's width is 2 × lysosomes − peroxisomes, because it is tested after the peroxisome band. It vanishes when peroxisomes ≥ 2 × lysosomes, and its lost share goes to "no target", sending the radical back to its maker. | FreeRadical.as, update-chances |
| D22 | **Slot 2 level-file hygiene.** It deactivates an objective that doesn't exist, uses one objective id twice, leaves a stray comment terminator as text, shows two objective labels that are blank or "???", and labels an objective "all" for a target of 1. | Cosmetic, except the "all" label, which misstates the goal. | level_02.xml |
| D23 | **Worn-out and used units refund.** A peroxisome that dies and a DNA repair enzyme that has healed both go through the recycle-with-refund path. | 50 AA + 2 FA per dead peroxisome; 100 AA per used enzyme. It may be intended for the enzyme (like slicers, §H). | GameObject.as, on-damage-kill and on-death; CellObject.as, on-recycle; DNARepairEnzyme.as |
| D24 | **The membrane cap ignores growth in progress.** The 25-node check counts only fused nodes. | A student can overbuy; a vesicle arriving at 25 loses its 100 ATP. | Cell.as, buy membrane |
| D25 | **Auto-replace ordered before the dead peroxisome leaves the count.** The replacement is bought, then the dead unit is subtracted from the ordered count. | At the cap of 10 the replacement fails "at maximum", with an alert. A clear failure. | Cell.as, kill peroxisome; Engine.as, replace peroxisome |
| D26 | **The slot 4 FA tip is wired to any FA shortfall** and never disarmed. | It can show long after Divide, on any purchase refused for FA. Harmless: the tip is true each time. | level_04.xml, FA tip objective |
| D27 | **Recycling the only chloroplast in slot 4 makes the level unwinnable.** The Divide objective needs 2, and Divide needs a parent. | A soft-lock; S2 is the only guard. | level_04.xml, divide objective; Cell.as, chloro screwed check |
| D28 | **An interrupted processing animation loses its input.** A producer's output is credited by a script on the processing animation's last frame, and the input was charged when that animation started. Damage, Divide or Recycle plays a different animation, replacing the processing one, so its last frame never plays. (Added, §L.) | The glucose (or FA) is spent and no ATP or heat arrives. Levels 1–2 can't trigger it: nothing there damages the mitochondrion, and Divide and Recycle aren't offered. Level 3's peroxide can damage a processing mitochondrion; Level 4 adds Divide and Recycle; the chloroplast shares the code from Level 5. **Not decided here:** each slice that introduces damage, Divide or Recycle for a producer states what happens to a cycle in progress, as an explicit outcome independent of any animation. | ProducerObject.as, produce loop and finish-process; GameObject.as, play-animation; the damage display routines |

## G. Corrections from the level-list cross-check

Added 2026-09-28 by [Reconcile the Baseline into the v1 level list](../tickets.md#ticket-13), after a second read of the fork's level files and source. Slot numbers are fork slots (shown level = slot + 1). Where this section and §8 disagree, this section wins.

**§8 level table**
- **Slot 0.** The 15 membrane nodes in §8.5 come from a code default: the level file declares 0, and the field is ignored (D10).
- **Slot 2.** The recycle beat means recycling *all* 50 starting slicers. The glucose and amino-acid pools stay off until then, and then a ring of 20 glucose gems (15 G each) appears. Radicals are switched on *mid-level*, at the nucleus-damage beat. **Added (§I):** before that, the shrink beat's actions send a scripted burst of 5 radicals from the mitochondrion aimed at the nucleus (25 damage); production starts only after the nucleus-damage tutorial closes. The NT and FA pools are declared but never activated, the Golgi stays hidden all level, and the ER's auto-peroxisome toggle is never offered.
- **Slot 3.** Declared but never sent: injector 10, invader 15, invader 15 (half wrapped). The ER, mitochondrion and peroxisome menus are rebuilt at load, which also grants the peroxisome "move" action (missing from §8.1).
- **Slot 4.**
  - The glucose pool is 150 gems, active, with regrowth chance 0, so it is a fixed pool.
  - Declared but never sent: injector 20, infester 7.
  - "Take defensin" is granted only after the first wave is defeated.
  - The first chloroplast is spawned by the level's first objective at start, about 1.05 × lens away. It takes roughly 25–30 s to arrive if the player waits. **Corrected (§K):** it is spawned only after the opening dialog closes, by the `start` objective's actions.
- **Slot 5.**
  - The 10% and 20% vesicle wraps apply only to invaders and infesters; the injectors in those trios have none.
  - Pools: glucose declared but never activated; NA 30 gems at 0.05, active; AA 250 gems at 0.8, active; FA never activated.
  - Infester 5 is sent immediately when the invader wave is defeated, with no countdown.
  - Declared but never sent: injector 30, invader 55.
- **Slot 6.** The glucose pool is 500 gems with regrowth chance 0. The AA pool is inactive. A second, inactive 50-gem glucose entry in another section is presumably ignored.
- **Slot 7.**
  - Toxin thresholds 10 and 25 send an injector + invader *pair*, not a trio. Trios go out at 50 and 90, and a single injector at 75. A threshold at 5 sends nothing.
  - The parallel timer chain is **finite**: 11 sends, the last (35 injectors, 60 invaders, 35 infesters) at about 1,020 s. It then starts a 60 s countdown that fires nothing, since completed objectives never re-fire.
  - Starts with 0 lysosomes and 0 slicers.
  - Declared but never sent: wave groups 0, 3, 4, 6 and 7.

**Built but never offered to the player** (none is Baseline behaviour, since no level exposes it)
- **Lysosome pop:** recycles the lysosome and dumps its acid into the cytoplasm, which damages everything outside vesicles. It is in the default action list, but the Engine's lysosome list offers only move and recycle.
- **Necrosis** as a player action: it starts the lysis sequence. It is commented out of slot 5's level file and the nucleus list.
- **Mitosis** (§8.3): it also sits in the nucleus's own default list, overridden by the Engine lists. It costs nothing.
- **Apoptosis**, organelle **Repair**, **Use Fatty Acids** (burn FA) and **bleb**: buttons, cursors or costs with no working handler.
- **Basal body** (cilia or flagellum): a centrosome action that was commented out. The centrosome's action list is empty.
- ER upgrade, attach and detach, make vesicle, and make Golgi: labels or stubs only.

**Level 8 flavour.** The slot 7 story talks about digestive fluid leaking in. That is flavour: no level changes the cytosol pH, and no pH meter is shown (the pH HUD is hidden).

**Draft-AC features with no footing** (confirmed by a full read of the walkthrough files and the fork source): heat shock proteins (only the slot 6 level *name*), Viral Protease, temperature effects on play, cold-adapted infesters, centrosome upgrades (beyond the cut basal body), membrane-tension penalties, ER "Expand Storage" (the likely source is ER "Expand Membrane"), a lipid-overload alert, antibodies, a cell wall and thylakoids.

## H. Corrections from the Level 2 spec

Added 2026-09-29 by [Specify Level 2 — Nucleus](../tickets.md#ticket-63), from a read of fork slot 1's level file and the source (and the uncompressed Flash library `cellgame2/LIBRARY` for animation lengths, which may not be the exact shipping file, so its frame counts are medium confidence). The affected sections above are corrected in place and marked "§H". Where this section and an earlier one disagree, this section wins.

- **Frame rate.** The Flash document declares 30 fps (answers gap B's frame-rate question). All frame counts here are Baseline frames at 30 Hz.
- **Rewards (§1.2, §5.3).** No reward is paid per kill. The wave-defeat payout is rounded to whole numbers and credited when the player dismisses the reward screen.
- **Mitochondrion (§1.3).** Fuel is charged at the start of a cycle and ATP credited when the processing animation ends. The fat backup needs FA ≥ 1, G < 1, and ATP strictly below 250.
- **Fat processing (§1.3, §7.3).** Defaults on; surplus glucose turns into fatty acids in every level that runs cytoplasm processing.
- **Repair and surplus ticks (§2.3, §1.3):** 61 frames, the same off-by-one family as D16.
- **Slicers (§3.1):** single-use, with their AA refunded.
- **Level 2's damage sources.** An injector landing does no damage; each escaping virion does 1. Nothing in slot 1 damages the nucleus, the mitochondrion or ribosomes (a hijacked ribosome is only busy).
- **Waves (§5.3):** the first spawn ring is at 1.2 × lens; the dormant timer ticks every 31 frames and resets on every escape and birth. An automatic zoom-out to scale 0.2 over 30 frames happens when a wave is sent and none is active.
- **Surround placement.** A scripted gem's radius is (1.05 + 0.25 × distance) × the lens radius, so distance 1 is 1.30 × lens and distance 0 is 1.05 × lens.
- **Ribosomes.** A ribosome's nucleotide is a permanent cost, not a temporary RNA cost, and the cost panel doesn't mark it with the temporary star. Ribosomes count the frame they are generated.
- **Camera.** Screen scale = 0.1 + 2 × the "zoom scale"; a level's `set_zoom` value is the zoom scale, clamped to at most 0.9 (scale 1.9). The player's range is scale 0.1–2.1. The mouse wheel zooms even while the zoom widget is hidden.
- **Triggers (§7.2).** An Objective's pre-actions run when it **completes**, before its tutorial; its actions run after the tutorial closes. The `delay` attribute is game frames between the condition being met and completion. `show_action_cost` fires on mouse hover over an action button. `virus_dormant` fires when a wave's line has no live virions or viral RNA and a dormant count above 0.
- **§8.1:** the slot 1 mitochondrion has its full default menu.
- **New defects:** D18 (partial batches) and D19 (pores never returned), in §F.

## I. Corrections from the Level 3 spec

Added 2026-09-29 by [Specify Level 3 — Membrane](../tickets.md#ticket-64), from a read of fork slot 2's level file and the source (animation lengths from the uncompressed Flash library, medium confidence, as in §H). The affected sections above are corrected in place and marked "§I". Where this section and an earlier one disagree, this section wins.

- **Ribosomes (§1.4, §3.1, D19).** Players can never recycle ribosomes: the ribosome's action list is empty in every level. Slot 2 starts with 25 ribosomes and 50 slicers made instantly, occupying both caps, so every slot 2 ribosome purchase fails "at maximum". D19 matters only where a level starts below the cap.
- **Vesicle routes (§3.2).** Peroxisomes bud straight from the ER; membrane vesicles go ER → membrane. Only lysosomes, defensins and toxin go through the Golgi.
- **Membrane (§2.2).** A vesicle arriving at a full membrane loses its 100 ATP. Shrinking has a 31-frame cooldown, lowers current HP only above the new max, and on the unpaid D3 path still fires its trigger and reports success.
- **Recycling small units (§1.4).** The ATP charge is taken at the click, per unit; the building blocks are refunded when the unit's recycle animation ends (about 10 frames for a slicer). A group stops at the first unit ATP can't cover, keeping the ones before it. No lysosome is needed.
- **DNA repair and peroxisomes (§3.1, D23).** The enzyme idles unless the nucleus is damaged or infested, and refunds its 100 AA after healing. A peroxisome takes 5 from a radical that targets it, refunds 50 AA + 2 FA when it dies, and auto-replace defaults off.
- **Radicals (§6).** The no-target band and self-targets send a radical back to its maker; the nucleus's share is 23.8%; radicals never expire; speed 2.5 px per frame easing to 2. A switched-off or damaged producer makes none. The first cycle after production switches on makes none.
- **The mitochondrion in slot 2.** Toggle is free; off means no glucose burned, no ATP, no fat burned and no radicals, and the cytoplasm does not take over (glycolysis runs only with zero mitochondria). Move costs nothing up front and is charged continuously. Below 50% health it stops producing for good in slot 2 (no heal, recycle or divide there); below 25% the "no working mitochondrion" dialog appears.
- **Slot 2's NT budget.** The NT pool never activates and viral RNA refunds nothing, so NT is a closed 10 until wave 2's reward (1 NT, the 0.5 rounded up; the walkthrough shows it).
- **Slot 2's objectives.** The "select all slicers" Objective completes on selecting one slicer. The nucleus-damage Objective listens for a one-shot event, so a burst hit landing before it is armed would stall the level (latent; the default geometry makes it unlikely). The two expansion Objectives and the three absorption Objectives are separate event-counted Objectives.
- **Failure alerts (§4).** "No nucleolus pore" and "no nucleus pore" show no alert (D20).
- **New defects:** D20–D24, in §F.

## J. Corrections from the Level 4 spec

Added 2026-09-29 by [Specify Level 4 — Golgi and lysosomes](../tickets.md#ticket-65), from a read of fork slot 3's level file and the source (animation lengths from the uncompressed Flash library, medium confidence, as in §H). The affected sections above are corrected in place and marked "§J". Where this section and an earlier one disagree, this section wins.

- **Pools (§1.2).** Regrowth has no ceiling. Slot 3's glucose and AA pools (100 each at 0.3) are active from load; its NT and FA pools are declared but never activated.
- **Organelle recycling (§1.4).** For a mitochondrion or chloroplast:
  - the charge is taken at the click;
  - a digestion vesicle calls the nearest ready lysosomes, up to a number derived from the drawn size (about 8 for a mitochondrion, possibly 9);
  - nothing is digested until all have fused, and the vesicle waits and re-calls every 61 frames;
  - the acid does about 11.8 HP every 31 frames;
  - the refund is credited when the 31-frame recycle animation that follows its death ends;
  - the lysosomes bud back out (borrowed, not used up);
  - Cancel Recycle returns the lysosomes but not the charge, and the organelle keeps its acid damage (a working mitochondrion falls below 50% on the 5th tick).
- **Divide (§3.1).** No health check: a damaged mitochondrion can divide, and the daughter has full health. It takes 40 frames. The cap of 10 counts damaged mitochondria.
- **Lysosomes (§3.1, §3.2, §5.2).**
  - A lysosome counts toward objectives from the start of its 90-frame grow animation at the Golgi.
  - It eats one virus per 80-frame meal and survives.
  - Lysosomes also eat newborn virions of every type on their way out.
  - One radical destroys a lysosome.
- **Wave rewards (§5.3).** AA and FA use the per-RNA spawn count, not offspring per virion. Slot 3's rewards are 1 NT + 13 AA (5 invaders), 1 NT + 25 AA (10 invaders) and 75 AA (15 injectors).
- **D21 (§6, §F).** The lysosome band's width is 2 × lysosomes − peroxisomes; the lost share goes back to the maker.
- **Slot 3 (§8.1, §G).** The load-time menus are listed in §8.1. The Golgi is **not engulfed**: it is revealed in place from a "New Organelle" pop-up. The damaged mitochondrion floats in from 1.05 × lens with 10 of 100 HP and never produces. The recycle objective counts "mitochondria ≤ 1", so recycling either mitochondrion completes it. D11's misspelling makes the lysosome count objectives complete only on a later lysosome event.
- **Walkthrough cross-check.** The lysosome "cap" of 4 and then 16 is the HUD's made/ordered display (`n / p` when they differ); the cap is 25. The same applies to the slicer "0/10".

## K. Corrections from the Level 5 spec

Added 2026-09-29 by [Specify Level 5 — Chloroplast](../tickets.md#ticket-66), from a read of fork slot 4's level file and the source (animation lengths from the uncompressed Flash library, medium confidence, as in §H). The affected sections above are corrected in place and marked "§K". Where this section and an earlier one disagree, this section wins.

- **Chloroplast production (§1.3, §6).** Not once a second. The counter must pass int(30 ÷ sunlight) frames; the process animation (about 52 frames) then plays with the counter frozen, as every producer's does, and 1 G is credited at its end: about 83 frames per G at sunlight 1, 113 at 0.5, 203 at 0.2, 353 at 0.1. Medium confidence, under verification in [Verify the chloroplast's production cycle](../tickets.md#ticket-100). It costs nothing to run. The 45-frame default period is never observable.
- **Chloroplast radicals (§1.3, §6).** Exactly 1 per cycle after the first, emitted at the cycle's start: about 2.3× a mitochondrion's rate at sunlight 1. The 25% chance field and the "up to 5" comment are dead code.
- **Chloroplast (§3.1).** 100 HP; stops producing for good below 50. Divide costs [200, 5, 100, 25, 10], has **no health check** (a full-health daughter, 75 px away), takes 40 frames, and the cap of 10 counts damaged ones. Recycling charges 100 ATP and refunds 5 NA, 100 AA, 25 FA, 10 G, through the lysosome digestion vesicle (§J).
- **Sunlight (§6).** A level-wide value; each new chloroplast takes it on creation. The HUD clamps the display to 0–100% only. In slot 4 it is set to 1 at load and never changed: the sun-lamp line has no action behind it.
- **Slot 4's chloroplast arrival (§G).** Spawned by the `start` objective's actions, after the opening dialog closes, at 1.05 × lens, homing on the centrosome at about 1 px per frame (about 26–28 s to a waiting cell); never culled for distance. It counts at engulfment. The next dialog opens 45 frames later, and the sunlight and radical dialog 200 frames after that one closes.
- **Slot 4's objectives.** The two radical chains are 5 and 10 one-step objectives, each completed by any peroxisome absorbing any radical. The fat objective is FA ≥ 10, checked when armed, so it can complete at once; there is no player "make fat" action. The ATP ≤ 500 tip fires once, on the first ATP change at or below 500 after the fat step starts, whatever the mitochondrion's state. The defensin objective counts **fused** defensins, and D11 means it waits for the next fusion.
- **Slot 4's pools (§G).** Glucose 150, active, no regrowth; AA 250 at 0.5, active; NA 50 and FA 50 declared, never activated.
- **Slot 4's waves (§8, §G).** Invader 10, then invader 15 with **7** wrapped (int(15 × 0.5); the first 7 spawned); each re-attack wraps int(pool × 0.5) again. Rewards: 1 NA + 25 AA, then 2 NA + 38 AA + 2 FA. Injector 20 and infester 7 are declared but never sent.
- **Wrapped viruses (§5.1).** They skip the defensin roll, shed the vesicle, make no entry wound, and their offspring inherit the wrap and still do 1 damage each on the way out.
- **Auto-replace (§3.1).** Only a peroxisome that dies from damage is re-ordered, once, at full price, with no retry; a failure shows the normal alert and fires the not-enough trigger. **Order:** when the dead peroxisome's recycle animation ends, its refund (50 AA + 2 FA; no ATP charge) is credited first, then the replacement is bought, then the dead unit leaves the ordered count, so at the cap it fails (D25) (Cell.as, on-recycle and kill peroxisome; Engine.as, recycle-something).
- **Defensins (§2.5).** Removal on recycle is immediate; slot 4 offers it after its first wave.
- **S2 (§4).** Also opens when the last chloroplast is removed; recycling the only one in slot 4 is a soft-lock (D27).
- **Slot 4's starting ribosomes.** 30, above the cap of 25: every ribosome purchase fails "at maximum" all level.
- **Endosymbiosis.** The fork has no in-level small print. Its only origin text, an encyclopedia science entry, says "evolved" and wrongly places chloroplasts in "some bacteria"; its dialog says chloroplasts are only in plant cells (algae and other protists have them). None of this is carried over.
- **Walkthrough cross-check.** The starting FA 10, G 0 and 0 peroxisomes (and ATP 957) disagree with the fork's [1000, 10, 550, 30, 50] and 3 peroxisomes; the fork rules on numbers. The chloroplast menu also has Toggle. The "sun lamp adjusted" is story only. "Half" wrapped is 7 of 15. The walkthrough's open questions are answered: dilution is defensins ÷ nodes (cap 0.5); the chloroplast radical rate is 1 per cycle; the defensin recycle refund is 50 AA + 1 FA for 50 ATP.
- **New defects:** D25–D27, in §F.

## L. Corrections from verifying the mitochondrion's cycle

Added 2026-09-30 by [Verify the mitochondrion's production cycle](../tickets.md#ticket-85), from the fork's source, its uncompressed Flash library (`cellgame2/LIBRARY`), and a read-only parse of the compiled `cellgame.swf`. The affected sections above are corrected in place and marked "§L". Where this section and an earlier one disagree, this section wins.

- **Frame rate (§1.3, gap B).** 30 fps. The Flash document declares it (§H), and the compiled game's header says 30.0.
- **How the mitochondrion's cycle runs (§1.3).** Game logic runs once per frame while the level is in play; a tutorial's faux-pause stops it, and every count and animation below with it.
  - **Counting.** Each frame the counter goes up by 1, but only while the mitochondrion is switched on, undamaged, and not playing a timed animation (processing or dividing). When it passes 45, on the 46th frame, it resets to 0 and tries to charge 1 G. The reset happens even if the charge fails.
  - **Processing.** A successful charge starts the processing animation. The game steps that animation by hand, one frame per frame of game logic, starting on the frame after the charge. The script that credits 38 ATP sits on the animation's last frame, frame 52, so **the credit comes 51 frames after the charge**. The credit is immediate in the resource totals; the flying icons are only a display.
  - **Credit to credit: 46 + 51 = 97 frames, about 3.23 s**, while fuel lasts. The old 1.5 s figure was the 45-frame counter alone.
  - **The first cycle.** Each new mitochondrion, including a daughter from Divide, starts its counter at a random whole number from 0 to 44, each equally likely. Its first charge comes 2–46 frames after it starts counting, and its first credit 53–97 frames after (about 1.8–3.2 s). Several mitochondria therefore produce out of step.
  - **No fuel.** When the counter fires with no G, and the fat backup doesn't apply, nothing is charged and counting starts again. Glucose that arrives then waits up to 46 frames to be charged.
  - **Fat backup.** Uses the same animation, so the same 51-frame wait and 97-frame cycle.
  - **Switched off.** The counter freezes where it is; an animation already running still finishes and credits.
  - **Divide.** Its animation (40 frames long) also freezes the counter. Starting it during processing is D28.
- **Cytoplasm glycolysis (§1.3).** 61 frames, not 60 (the counter must pass 60; the same off-by-one as D16), credited at once.
- **Evidence and confidence.**
  - *Read directly:* the 45-frame period, the "must pass" test, the reset, the random starting count, the frozen counter during a timed animation, the one-frame-per-frame stepping (fork source); the 52-frame processing animation with its credit script on frame 52 (library).
  - *Corroborated:* the compiled game has one six-state organelle symbol whose processing clip is 52 frames and divide clip 40, matching the library's mitochondrion. It carries no bytecode, so the script's frame position comes from the library alone.
  - *Inferred:* the animation isn't stepped on the charge frame, because a listener added while an event is being dispatched isn't called in that dispatch (standard Flash behaviour). How the player orders a frame script within its frame can't move the credit to another frame, so the 97 frames stand either way.
  - *Not verifiable here:* that the published fork matches the commercial build in the walkthrough, and the frame rate students actually got. The Baseline is counted in frames.
- **Walkthrough cross-check.** In Level 1, from [02:37] to [03:15], ATP goes 700 → 1005 (+305, 8 credits of 38) while glucose never runs out. The old 1.5 s figure predicts about 25 credits in that window, and 97 frames about 12. The observed 8 is fewer than either. A tutorial hold in the window, a recording below 30 fps, or coarse timestamps could each explain the shortfall, so **the video can't reliably distinguish the two timings**. The fork is the basis for adopting 97 frames.
- **New defect:** D28 (an interrupted processing animation loses its input), in §F.

## M. Corrections from purchases and production

Added 2026-10-10 by [Build slice: purchases and production](../tickets.md#ticket-120), from a read of the fork's source (`Engine.as`, `Cell.as`, `Ribosome.as`, `RNA.as`, `SlicerEnzyme.as`, `EvilRNA.as`, `PoreMatrix.as`, `Costs.as`, `Act.as`) and the animation timelines in the XFL library (`cellgame2/LIBRARY`, medium confidence, as in §H). Frame counts are Baseline frames at 30 fps. A frame script that fires on timeline index N completes N frames after the animation starts. The build's plan is `docs/plans/purchases-production.md`.

**Buying** (`Engine.as`, the basic-unit purchase; `Costs.as`)
- Each press asks for a batch of 5 and buys one unit at a time until one fails (D18). Units already bought stay bought.
- **Checks per unit, in order.** Ribosomes: the ordered count must be below 25, then the nucleolus must have **more than 1** ready pore, then the cost is paid. Slicers: the ordered count must be below 50, then the cost. The slicer's pore check is commented out.
- **The ordered count** is units ever paid for or started with, less units lost. It never drops when a unit is delivered, only when one dies or is used up. Caps therefore count present plus in-production units (D19).
- **The pore check reads the ready list as it is at the press.** Pores are taken only when ribosomes are made, on later frames, so every unit of one press sees the same list.
- Failure alerts: over the cap, "already at the maximum"; can't afford, a "not enough …" message; no nucleolus pore, an empty message (D20).
- Buy buttons are never greyed out. Affordability is checked only on the press.

**Delivery: the queues** (`Engine.as`, transactions)
- Each frame releases at most one ribosome and at most one slicer mRNA from the paid queues, starting the frame after the purchase. A ribosome and an mRNA can leave on the same frame.

**Ribosomes** (`Cell.as`, ribosome generation; `PoreMatrix.as`; `Ribosome.as`)
- Each is made at a random ready nucleolus pore, 10 px above it, and counted on that frame.
- The pore moves to the busy list without its opening animation, so it never returns (D19).
- With no ready pore, a random busy pore's spot is used and the lists don't change. There are 24 pores.
- It grows for **29 frames**. Then it moves to a random spot 90–110 px from the nucleus's centre at **4 px a frame**, at no ATP cost. It arrives within √(2 × 4) px, and only then can it take an mRNA.
- Ribosomes then stay put. Only the membrane and pseudopods shift them. In the fork they can't be selected.

**Slicers' mRNA** (`Cell.as`, mRNA generation; `RNA.as`; `Ribosome.as`)
- Each mRNA appears at a random ready nucleus pore, which plays a **47-frame** opening. When an opening ends, the **oldest** busy pore returns to the ready list, not necessarily the one that opened. With no ready pore, a busy pore's spot is used at once and no opening plays.
- It claims the nearest ribosome that is ready, free and not doomed, at once. With none, it asks again every **61 frames**. With no ribosome at all, the fork alerts.
- It grows for **35 frames**. Then it travels to its ribosome at **2 px a frame**, re-aiming every 16 frames, and docks within 2 px. The mRNA always travels; the ribosome never comes to it.
- **44 frames after docking**, on one frame and in this order:
  1. the slicer is made within 12.5 px of the ribosome (on each axis), and counted;
  2. the ribosome is free again, where it is;
  3. the strand breaks down and returns its 1 NT.

  The 44 frames are the strand's 14-frame thread animation and its 30-frame breakdown animation. The 1-frame dock animation is replaced at once.
- The ribosome's declared busy time (30 frames) and processing timeout (150) are dead code. The only live timeout is the strand's 300-frame docking limit, which can't fire for enzymes.

**Slicers** (`SlicerEnzyme.as`, `EvilRNA.as`, `Cell.as`)
- A new slicer moves at **3 px a frame** to a random spot 170–205 px from the nucleus's centre and stays there. It doesn't wander. (Its set-up runs twice in the fork; harmless.)
- **Starting units** (a level's starting ribosomes and slicers) take no pores and play no growth. Each is placed at once on its ring and counted as both ordered and present. A start at or above the cap therefore refuses buying until units are lost.
- **Hunting.** Viral RNA calls the nearest slicer that has no target on its first frame, then every **16 frames**, so several slicers can converge on one strand. A hunting slicer moves at **3 px a frame**, re-aims every **6 frames**, and cuts within **25 px**.
- **The cut.** The strand stops at once and plays a 26-frame dying animation, returning 0 NT. The slicer is used up **10 frames** later and refunds its 10 AA, with no ATP. Both the ordered and present counts drop by 1.
- If its strand goes, every slicer still hunting it moves to a new spot on the ring.
- Viral RNA can't be cut once it has finished threading at a ribosome (for the viruses slice).

**Animation lengths** (library timelines): nucleus pore opening 47; mRNA growth 35, thread 14, breakdown 30, dying 26; ribosome growth 29, ER dock 29, processing loop 20, recycle 39; slicer used up 10.

