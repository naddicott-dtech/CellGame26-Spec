# Level 3 — Membrane (working title)

Players see the title **Room to Grow** (`docs/story.md`). This file is the full spec, decided with the teacher in [Specify Level 3 — Membrane](../tickets.md#ticket-64).

It follows [`spec-template.md`](spec-template.md): the build contract comes first and the rationale is in the appendix. The rules for every level are in [`docs/levels.md`](../levels.md), the shared numbers and UI rules in [`shared-defaults.md`](shared-defaults.md), and terms follow `CONTEXT.md`. [`level-2.md`](level-2.md) is the most recent worked example.

**Units.** Every frame count and every per-frame quantity here is in **Baseline frames at 30 Hz**, the fork's rate. The core runs at 60 Hz, so 1 Baseline frame is 2 core ticks. The conversion covers **everything measured per frame**, not only timers:
- durations and delays double in ticks (25 frames are 50 ticks);
- speeds halve per tick (a speed of 2.5 px per frame is 1.25 px per tick);
- per-frame charges, damage intervals and easing steps are converted the same way.

The core converts in one place, and a unit test checks that each moving thing covers the Baseline's distance over 30 Baseline frames. Never mix the two units.

## Summary

| | |
|---|---|
| Tier | Required. **LT1 is complete by the end of this level.** |
| Teaches | **LT1:** fatty acids are used to make membrane lipids (not a chain of them); surplus glucose becomes fat. **LT2:** the ER makes membrane; peroxisomes break down hydrogen peroxide; DNA repair enzymes mend damaged DNA, and enzymes work repeatedly. **Experienced:** recycling returns building blocks but not the ATP spent; peroxide damages the organelle that makes it. |
| Objectives | 19 (beats table) |
| Waves | Injectors: 15, then 25 |
| Won | Wave 2 defeated |
| Lost | Membrane at 0 (lysis), nucleus at 0, or Starvation. This is the first level where the nucleus can be damaged. |
| Dead End | Possible: the declared predicate *No peroxide source* classifies as a Dead End when ATP is at or above the Starvation Line (see *Win and loss*). Three more candidates await validation in the build. |
| Level Rating | The shared rules, from 2 waves weighted 15 and 25 |
| In-level time | About **12 min** for a typical student (working range about 11–14 min): about 4 min of play and about 8 min of teaching. Provisional; checked by playtest. |

Level 3 is the Baseline's early difficulty spike: in the teacher's experience of the Flash game, a subset of students get stuck here. Playtests record **where students get stuck and whether they recover**, not only the time.

## Shared infrastructure used

This section links to the shared systems; don't rebuild any of them for this level. The list comes from [`infrastructure-index.md`](infrastructure-index.md).

| Shared system | Where | Level 3 specifics |
|---|---|---|
| Game Shell state machine | `architecture.md`: Game Shell | Pause and the encyclopedia exist from the first frame ([shared defaults](shared-defaults.md#pause-how-to-play-and-the-encyclopedia)). |
| Objectives, and the minimum beat | `architecture.md`: Simulation Core; [shared defaults](shared-defaults.md#objectives-completion-and-the-linger) | 19 Objectives. Several are **state predicates** so work that lands during a completion linger isn't lost (Beats). |
| Story Hold | Same; [shared defaults: Story Holds](shared-defaults.md#story-holds) | 24 holds, 2 of them mid-Objective, and 2 in the armed gap before Objective 10 (beats table). |
| Game Events | `architecture.md`: Game Events | Selection, recycling, purchase, fusion, move, toggle, peroxide hit and absorption, heal, and wave events feed Objectives and the watchdog. |
| Loss Routes, Starvation | [`levels.md`: shared end-of-level rules](../levels.md#the-shared-end-of-level-rules) | **Starvation is on**, with the shared values. All three Loss Routes can be live; their answers are in *Win and loss*. Starvation advice is keyed to introductions (below). |
| Win-impossible predicates, Dead End | Same | One declared predicate (*No peroxide source*) and three candidates (Win and loss). |
| The Restart button | Same; [shared defaults: Restart](shared-defaults.md#restart) | The shared rule. No struggle offer. |
| Idle watchdog | [shared defaults](shared-defaults.md#the-idle-watchdog) | Progress and highlight per Objective (beats table). |
| Spark preview and refusal | [shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) | Used unchanged. The lens grows with the membrane, so the reach and the most a pull can cost grow too. |
| Tuning | `architecture.md`: Content; [shared Tuning](shared-defaults.md#tuning) | This spec's Tuning section. No shared value is overridden. The DNA repair enzyme's **repair jobs** value is added to the shared table (it's shared, not per level). |
| Level Rating | [`levels.md`](../levels.md#the-level-rating) | Waves of 15 and 25. |
| Session Log | [Define the Session Log and save format](../tickets.md#ticket-15) | Also records group selections, recycling (partial runs included), refusals, moves and toggles. |
| The adapters | `architecture.md`: Renderer | No new adapter. Peroxide uses the particles adapter. |
| Selection and touch targets | [shared defaults](shared-defaults.md#touch-and-selection) | Slicers, the nucleus, the mitochondrion, the ER, DNA repair enzymes and peroxisomes are selectable. The ER is claimed by tapping it. Group selection is an extension (below). |
| The world edge | [shared defaults](shared-defaults.md#the-world-edge) | Level 3 declares its own geometry and look (Starting state). |
| Left-behind gems | [shared defaults](shared-defaults.md#gems-not-drawn-from-a-pool) | The glucose ring credits its pool (Scripted spawns). |
| Balance Runs and the stuck-state search | [`levels.md`: Testing](../levels.md#testing) | Verification. |

**Baseline simulation systems used here for the first time.** These need no design ticket, because the catalogue (`docs/research/baseline-mechanics.md`, corrected in §I) fixes them. They are shared systems, listed in the infrastructure index and owned by the build, **never implemented privately inside Level 3**:
- small-unit recycling (§1.4, §I)
- surplus glucose to fat, now taught (§1.3)
- membrane growth and shrinking, and the lens that follows the node count (§2.1, §2.2, §I). **The lens size is core geometry:** the pull clamp, the spark preview, spawn placement and the camera all read the core's value; none computes its own.
- hydrogen peroxide: production, targeting, damage (§1.3, §6, §I)
- peroxisomes (§3.1, §I)
- DNA repair enzymes (§3.1, §I), with the reuse Deviation below
- producer damage (§3.4): a damaged mitochondrion stops producing

**Extensions needed.** Each is a shared interaction contract. Each preserves the catalogue's gameplay rules and timing, and raises any proposed mechanical change separately. They block the Level 3 **build slices that use them**, not this spec (the map's *Sequencing* note). If one changes an Objective, a timing or an available player action here, that part of this spec is revisited.
- [Group selection and group actions: the shared interaction contract](../tickets.md#ticket-87): the visible "Select all of this type" action and its gesture shortcut, group actions with an explicit quantity, and ATP charged now shown apart from materials returned later.
- [Organelle Move and Toggle: the shared interaction contract](../tickets.md#ticket-88): choosing a destination, the continuous move cost, what an accepted move command is, and how an on/off action shows its state.
- Level 2's three contracts are used unchanged: [the camera](../tickets.md#ticket-82) (it consumes the core's lens size for framing and display), [Action Panel purchases](../tickets.md#ticket-83) and [wave presentation](../tickets.md#ticket-84).

**Open dependency:** [Verify the mitochondrion's production cycle and check the Starvation warning against it](../tickets.md#ticket-85). The metabolism row, and the peroxide rate that follows from it, aren't build-complete until it's resolved.

## Starting state

| | |
|---|---|
| Resources | **Starting resources** (Tuning) |
| Units | 1 mitochondrion, **25 live ribosomes and 50 live slicers**, with no pending production: both caps are occupied, and each unit counts once toward its cap. No ER, Golgi, peroxisomes or DNA repair enzymes. The ER arrives at Objective 5. The Golgi never appears in this level. |
| Actions | The nucleus: Buy Ribosomes (refused "at maximum" all level) and Buy Slicers. Every other action is enabled at its Objective (Beats). The mitochondrion has **no actions** until Move and Toggle arrive at their introductions. |
| World | The same lab petri dish as Levels 1–2, **declared again in Level 3's own content**, not inherited: the **dish radius**, world-fixed bands and a drawn rim, with the cell at the centre. Pulls are clamped by the **edge clamp**. |
| Membrane | 15 nodes and 100 HP max (**membrane**, Tuning), so the lens is about 1,191 px |
| Camera | The **starting zoom**, with the zoom control shown (it was revealed in Level 2) |
| HUD | The ATP, NT, AA and glucose meters, the membrane-health gauge, the Objective pane, Pause and the encyclopedia button. The FA meter is hidden until Objective 3 completes. |
| Pools | The glucose and AA pools (**pools**, Tuning) are **off until the recycle gate** (Beats). There is no NT or FA pool in this level. |
| Producers | The mitochondrion burns glucose from the start (**metabolism**, Tuning). Cytoplasm glycolysis doesn't run, because the cell has a mitochondrion. **Fat processing is on** (Baseline). **Peroxide production is off** until the damage-reveal hold closes. |
| Movement | Free from the first frame. Pulls before the gate find nothing, because the pools are off. |

## Beats

Every row with a hold is a **Story Hold**. The scripts ticket ([Script the ground trials](../tickets.md#ticket-73)) writes the lines; this table fixes what each beat covers. "Mid" marks a hold that fires inside an armed Objective; "gap" marks one that fires while no Objective is armed.

| Story Hold | Objective | Completes when | Switches on | Movement | HUD reveals | Watchdog progress and highlight |
|---|---|---|---|---|---|---|
| **Start.** The cell needs a bigger, tougher membrane for the ride (`docs/story.md`). The viruses are gone, so the slicers can be recycled for their amino acids. On rails: selecting one slicer, then **Select all slicers**. | 1. **Select your slicers** | **Every slicer in the cell is selected**, by the visible action or the gesture shortcut | The "Select all of this type" action (group selection contract) | Free | — | Any slicer selected; a slicer, then the Select all action |
| **Recycling.** Recycling breaks a unit back into its building blocks. It costs a little ATP now, and the amino acids come back when recycling finishes (on rails). | 2. **Recycle all your slicers** | **No slicer is left** in the cell or ordered, **and every recycling refund has been credited** | Recycle on slicers, **when Objective 2 is armed** | Free | — | Each slicer recycled; the Recycle action |
| **Supplies.** Recycled amino acids can be reused. To grow, the cell needs fatty acids; the cytoplasm turns spare glucose into fat. | 3. **Collect glucose to make fatty acids** | FA ≥ 1 | **When this hold closes:** the glucose and AA pools, and the **glucose ring** | Free | The FA meter, **on completion**, before the next hold's cards (Baseline) | Each glucose pickup; the nearest glucose gem |
| *Mid:* **Fat.** When glucose first reaches the **fat threshold**. The cytoplasm is turning surplus glucose into fatty acids (scripted camera move, **fat zoom**). Turning extra glucose into fat is metabolism; fat is the longer-term store (accuracy rules 3 and 4). | (Objective 3) | — | — | — | — | — |
| **Fatty acids.** Fatty acids are the building blocks the cell uses to make membrane lipids; lipids are made *from* them, not chains of them. The fatty-acid encyclopedia entry unlocks. Scripted camera move back (**zoom back**). | 4. **Make 10 fatty acids** | FA ≥ 10 | — | Free | — | Each glucose pickup or FA gained; the nearest glucose gem |
| **The ER.** The cell has plenty of fat but needs an organelle to use it. The ER appears as a new thing and pulses. | 5. **Get the ER** | The student taps the ER, within the shared touch target, **and it has arrived** | The ER arrives (the Baseline's arrival, as the nucleus in Level 2) | Free | — | None; the ER |
| **ER.** The ER makes membrane, and growing the cell makes it tougher. How to make membrane. | 6. **Make some membrane** | A Make Membrane purchase **orders at least 1 unit** | Make Membrane, **when Objective 6 is armed** | Free | — | None; the Make Membrane action |
| **Vesicle.** How a membrane vesicle is made, on rails (**vesicle zoom** to the nucleus): the RNA instructions reach a ribosome, which docks at the ER; the protein and lipid go into the ER, which buds a vesicle; the vesicle travels to the membrane and fuses. **The RNA never turns into the product.** Foreshadowed tier. A short line (Aqua, or the encyclopedia's small print) says that in real cells, **membrane proteins travel from the ER through the Golgi** on the way to the membrane, and that the Golgi arrives next level. It doesn't claim that all new membrane takes that route (lipids also have routes that skip vesicles). | 7. **Watch the process** | Nodes ≥ 16 | The camera **zooms out** when a membrane vesicle first exists (**vesicle zoom-out**, play running; see below) | Free | — | Each pipeline stage reached; none (the student is waiting) |
| **Grown.** Growing the membrane widens the cell's view and adds health. A candidate line: Ursa's "Tardi-cardigan!" (`docs/story.md`). | 8. **Expand the membrane 2 more times** | Nodes ≥ 18 | — | Free | — | Each order or fusion; Make Membrane, **cleared** once nodes plus paid growth in progress reach 18 |
| **Recycling membrane.** Membrane can be recycled too, returning its building blocks. | 9. **Recycle 1 unit of membrane** | A **paid** Take Membrane is accepted by the core while armed (D3 fixed) | Take Membrane, **when Objective 9 is armed** | Free | — | None; the Take Membrane action |
| *Gap:* **Something's happening.** Something is going on at the mitochondrion. | — (the armed gap; see below) | — | **The peroxide burst**, the **burst delay** after this hold closes | Free | — | — |
| *Gap:* **What was that?** Opens the **mystery delay** after the burst first exists. Scripted camera move to the mitochondrion (**mystery zoom**). The green particles haven't been named yet. | — | — | — | Free | — | — |
| **Damage reveal.** Opens the **reveal delay** after the burst's **recorded hit** on the nucleus, and only after the previous hold has closed. Aqua spots peroxide damage in the nucleus: "That's the Settler genome!" Hydrogen peroxide is given off by the mitochondrion as it makes ATP; it damages DNA and organelles. Moving the mitochondrion away gives peroxide farther to travel. | 10. **Move the mitochondrion** | The core **accepts a move command** with a chosen destination (not pressing Move, not arrival; no minimum distance) | **When this hold closes:** peroxide production; Move, **when Objective 10 is armed** | Free | — | Pressing Move; the mitochondrion's Move action |
| *Mid:* **How to move.** The first press of Move while Objective 10 is armed. How to choose a destination, and that moving costs ATP all the way (Move and Toggle contract). | (Objective 10) | — | — | — | — | — |
| **Switch it off.** Farther away buys time, but to be safe, switch the mitochondrion off. | 11. **Switch the mitochondrion off** | The mitochondrion is switched off (a state) | Toggle, **when Objective 11 is armed** | Free | — | None; the mitochondrion's Toggle |
| **Off.** Switched off, it makes no peroxide, but no ATP either. Now check the nucleus for damage. | 12. **Inspect the nucleus** | The nucleus is selected | — | Free | — | None; the nucleus |
| **Damage.** The nucleus's health shows the damage to its DNA. DNA repair enzymes mend damaged DNA. | 13. **Build DNA repair enzymes** | The **first heal** of the nucleus while armed | Buy DNA Repair, **when Objective 13 is armed** | Free | — | Each order or heal; Buy DNA Repair, cleared once an enzyme is on its way |
| **Repair; three ways to die; peroxisomes.** On rails, the enzymes mend the DNA (repair shown on the DNA, not on the nucleus's boundary); each one works **several times**, then wears out and returns its building blocks. Then **the ways-to-die card** (below). Then: the cell needs something to deal with peroxide for good; the ER can make a peroxisome. | 14. **Make a peroxisome** | Peroxisomes ≥ 1 (a peroxisome counts from when it is created, Baseline) | Buy Peroxisome, **when Objective 14 is armed** | Free | — | Each order or arrival; Buy Peroxisome, cleared once present plus ordered reach 1 |
| **Peroxisome.** Peroxisomes break down hydrogen peroxide. Switch the mitochondrion back on. | 15. **Switch the mitochondrion back on** | The mitochondrion is switched on (a state) | — | Free | — | None; the mitochondrion's Toggle |
| **More peroxisomes.** The mitochondrion will keep giving off peroxide; peroxisomes wear out and need replacing. | 16. **Make 3 peroxisomes** | Peroxisomes ≥ 3 | — | Free | — | Each order or arrival; Buy Peroxisome, cleared once present plus ordered reach 3 |
| **Watch them work.** | 17. **Absorb 3 peroxide** (counts 3 → 0) | **3 absorptions while armed** | — | Free | — | Each absorption; the mitochondrion's Toggle **when it is switched off**, otherwise none |
| **Incoming.** Something pink at the dish's edge: viruses. Build slicers. | 18. **Survive the viruses** | Wave 1 defeated | **Wave 1** is sent when this hold closes | Free | The wave display (wave ticket) | Any drop in the wave's live plus dormant count; Buy Slicers, **suppressed** when the slicer cap blocks another purchase |
| **Ledger.** After wave 1's defeated card is acknowledged. R.Y.E. admits he brought the viruses in, opens his ledger of toast candidates and promises harder tests (`docs/story.md`). | 19. **Survive the second wave** | Wave 2 defeated | **Wave 2** is sent when this hold closes | Free | — | As Objective 18 |
| **Win.** After wave 2's card. The scientists resolve to prepare. Then the results screen. | — | — | — | Locked | — | — |

### When effects happen

The Baseline runs an Objective's "pre-actions" when it completes and its "actions" after its tutorial closes. Here, for every Objective:
1. **On completion** (the tick the predicate is met, after the minimum beat): the completion effects, and any HUD reveal marked *on completion*. The completion linger starts, with play running.
2. **When the linger ends**, the next row's hold opens.
3. **When that hold closes:** its *Switches on* effects apply (pools, spawns, production, wave sends), then the next Objective is **armed**, which is when its action is enabled.

The Baseline's per-Objective delays (mostly 45 frames) are replaced by the shared completion linger: an adaptation, not an equivalent timing. The only delays kept are the ones that time a demonstration: the **burst delay**, the **mystery delay** and the **reveal delay**, all on the gameplay clock, so a hold's reading time never advances a hazard. The playable build checks that the burst, the damage reveal, the supply gate and the wave sends land at these points.

### Notes on the beats

- **Actions are enabled when their Objective is armed** (Recycle with 2, Make Membrane with 6, Take Membrane with 9, Move with 10, Toggle with 11, Buy DNA Repair with 13, Buy Peroxisome with 14), and the core refuses each before then. Each stays available for the rest of the level. Buy Slicers is available from the first frame (Baseline): nothing needs a first-purchase Objective for it.
- **Objective 1 needs the whole group.** An on-rails demonstration followed by an Objective met by selecting one slicer wouldn't show the student can do it. Selecting never executes an action: the group then shows the ordinary Recycle action with its quantity ("Recycle 50 slicers") and total, with the ATP charged now shown apart from the amino acids returned later (group selection contract).
- **Objective 2 waits for the refunds**, so it can't complete between a slicer vanishing and its amino acids arriving. A student who rebuys slicers during Objectives 1–2 must recycle those too (Baseline); that costs ATP, and it's accepted rather than locked.
- **State predicates where a linger could lose work.** Production and growth run during completion lingers and other running intervals. Objectives 7 and 8 count **nodes**, and Objectives 11 and 15 check the toggle's **state**, so a fusion or a toggle during a linger isn't lost. A student who switches the mitochondrion back on early completes Objective 15 after its introduction and minimum beat.
- **Objective 17 counts absorptions while armed.** Absorptions during Objective 16's linger are lost toward it (the shared rule); peroxide normally keeps coming. If it stops for good, *No peroxide source* covers it.
- **The armed gap before Objective 10.** From the Something's happening hold to the damage reveal, no Objective is armed and play runs; the burst is in flight. This replaces the Baseline's two placeholder Objectives (a blank label and "???"), which only waited for events. The holds present **in order**: the burst appears → What was that? → the damage reveal → production and Move. The burst's hit on the nucleus is **recorded whenever it lands**: before the What was that? hold opens (during the mystery delay) or after it closes. Holds freeze gameplay, so no hit lands during one. The record is kept across the hold and after any later repair, and the reveal opens only after the What was that? hold has closed. A recorded hit, not "health below maximum", is the trigger, so the reveal can't be missed and can't be triggered by other damage. This removes the Baseline's latent stall, where a hit landing before its Objective was armed was missed and production never switched on.
- **The vesicle zoom-out runs with play running**, not in a hold (Baseline). If [the camera contract](../tickets.md#ticket-82) doesn't allow scripted moves outside holds, it moves to the Grown hold, and this spec is revisited.
- **The ways-to-die card** lists the three ways a cell can die: the nucleus at 0, the membrane at 0, and ATP too low for too long. It **always shows** at this beat: this is the first level where all three are live, and the student has just watched the nucleus take damage. It is a summary, **not** the first-time Starvation card: that card still opens at the first warning (once per save), because it explains the countdown in context. A student who met Starvation in Level 2 sees it here as a familiar item. The card joins How to Play.
- **Starvation advice keyed to introductions** (the shared table in `docs/levels.md`, corrected in place): "Check whether a mitochondrion is switched off" becomes available when the **Switch it off** hold (Objective 11's introduction) is acknowledged; "Mitochondria can burn stored fat" when the **Fatty acids** hold is acknowledged. Advice is keyed to the teaching beat, never to a level number. A damaged mitochondrion is explained by the loss hints ([hint rules](../tickets.md#ticket-80)), not by Starvation advice: Level 3 has no action that repairs it.
- **Wave-defeated cards** come from the wave ticket. Acknowledging one credits its **wave reward** exactly once, in the core, before the next hold opens. There is no escaped-virus hold (that was Level 2's teaching beat).
- **Zero slicers at wave 1 is possible, not guaranteed.** The recycle gate leaves 0 slicers, and Buy Slicers stays available, so a student can prepare during Objectives 3–17. That freedom is preserved: nothing clears slicers or blocks preparation. The Incoming hold says to build slicers; the approach from the first spawn ring is the unprepared student's build window. Frozen holds differ from Flash, so the build checks the unprepared case rather than assuming that window is enough, and adds no time in advance.
- **The first-time Starvation card** may open at any point where the warning first shows (shared rule).

## Scripted spawns and rules

### Scripted spawns

| Spawn | Placement | Visibility | Left behind |
|---|---|---|---|
| **Glucose ring** (Baseline) | The **glucose ring gems** at the **glucose ring radius**, around the centrosome's position when the Supplies hold closes, at *i* ÷ *n* of a full turn (D17 fixed), static | Just outside the lens when placed | Farther than the **return distance** from the centrosome: **credits the glucose pool by +1**; the gem's value is lost (Baseline) |
| **Peroxide burst** (Baseline) | The **burst count** of peroxide, around the mitochondrion, the **burst delay** after the Something's happening hold closes, each aimed at the nucleus | Visible | Peroxide never expires and is never left behind: each one hits the nucleus or is absorbed. It is not production, so the "no peroxide on the first cycle" rule doesn't apply. |

### Rules

| Rule | Trigger (armed between) | Effect | Cooldown and limits |
|---|---|---|---|
| **Pools** (Baseline) | From the Supplies hold's close to the end: a spawn roll per pool every **roll distance** of travel | One gem just outside the lens, at a random angle (shared pool rules). The chance per roll falls as the lens grows (count × 1,250 ÷ lens², Baseline). | Regrowth every **regrowth interval**. D15: a roll with no room makes no gem and costs nothing. |
| **Recycling small units** (Baseline, D2, D18) | Recycle on slicers, from Objective 2's arming | Per unit, the **recycle charge** is taken at the command; the unit's building blocks are credited when its **recycle animation** ends; the unit is removed. A group runs one unit at a time until ATP runs short, keeps what went, and reports the requested and recycled quantities and the reason honestly. | No lysosome needed. Refunds 100% (D2 kept). |
| **Purchases** (Baseline, D18, D24) | Each action from its Objective's arming (Beats) | The shared purchase rules: batches unit by unit with honest partial fills; only ordered units charged; the requested quantity, bought quantity, cost and limiting reason reported (Action Panel contract) | Caps count units **ordered** as well as present. The membrane's node cap counts **all paid growth through fusion** (D24). Ribosomes are refused "at maximum". |
| **Production** (Baseline, §3.2, §I) | Any order | Slicers and DNA repair enzymes are made at a ribosome; membrane vesicles go RNA → ribosome at the ER → vesicle → membrane; peroxisomes bud **straight from the ER** (§I) | The shared pipeline. NT in flight is the only throughput limit the level adds: NT is a closed budget until wave 2's reward, so at most as many RNA-built items can be in the pipeline as the NT held allows. |
| **Membrane growth** (Baseline, §2.2) | A membrane vesicle fuses | +1 node where the ring is most stretched, max and current HP +10, the lens recomputed by the core; an extending pseudopod is cancelled (Baseline) | At most **25 nodes** |
| **Membrane shrink** (Baseline; D3 fixed) | Take Membrane, from Objective 9's arming, above 15 nodes | **The core checks the shrink charge first.** If ATP is short, it refuses with the shared honest refusal, and no node is removed. If paid: the least-stretched node is removed, max HP −10 (current HP is lowered only if it's above the new max), and the refund is credited. | The **shrink cooldown**; a press during it gets the shared refusal instead of silence |
| **Slicers** (Baseline) | A live slicer and viral RNA | The slicer hunts and cuts the viral RNA, and is **used up**, refunding its AA | No ATP cost to attack |
| **Waves** (Baseline, §5) | Wave 1 when the Incoming hold closes; wave 2 when the Ledger hold closes | The **waves** (Tuning), presented by the wave contract | Escapees re-attack after the **dormant re-attack** time |
| **Membrane repair** (Baseline, §2.3) | Membrane below max health | Spends the **repair cost** for 1 HP every **repair interval** | Skips silently while ATP is below the repair cost |
| **Surplus glucose to fat** (Baseline) | Glucose at or above the **fat threshold**, checked every **surplus interval** | Converts the **surplus amount** of glucose into 1 FA | On all level |
| **Mitochondrion** (Baseline) | Switched on, not damaged, with fuel | Burns glucose for ATP; when G < 1 and ATP is below the **fat-backup ceiling**, burns 1 FA for the **fat-backup yield** | Its cycle length is [the timing ticket](../tickets.md#ticket-85). Switched off or damaged, it burns nothing, makes nothing and gives off no peroxide, and the cytoplasm does **not** take over (glycolysis runs only with no mitochondrion). |
| **Mitochondrion Move** (Baseline) | From Objective 10's arming | The **move cost**, charged continuously while it floats; the move stops if ATP runs out | Move and Toggle contract |
| **Mitochondrion Toggle** (Baseline) | From Objective 11's arming | On or off, free | — |
| **Peroxide production** (Baseline) | From the damage-reveal hold's close, while the mitochondrion is switched on and not damaged | The **peroxide rate** per production cycle, with none on the first cycle after production switches on | — |
| **Peroxide targeting and damage** (Baseline, §6, §I) | Each peroxide as it is made | Picks a target by the **targeting weights**; a peroxide that rolls its own maker, or no target, drifts away and then homes back onto its maker. On reaching a live target it does the **peroxide damage** and is gone. | Never expires. Never targets the membrane, the ER or ribosomes. |
| **Producer damage** (Baseline, §3.4) | The mitochondrion's health below the **stop threshold** | It stops producing **for good** in this level: nothing in Level 3 heals, recycles, divides or replaces it | The Baseline's "no working mitochondrion" dialog is replaced by the shared Restart rule (levels.md Deviation) |
| **DNA repair enzymes** (Baseline; reuse Deviation) | Bought from Objective 13's arming | Each waits until the nucleus is damaged (or infested), travels to it, and on arrival heals the **repair heal** (clearing infestation first). It does **repair jobs** jobs in all. Between jobs it stays by the nucleus: while the nucleus is still damaged (or infested), its next job lands the **job interval** after the previous one; when the nucleus is full, it goes back to waiting, and travels again the next time the nucleus is damaged. After its last job it wears out and credits its **enzyme refund**. | No cap (D5 kept). The Action Panel shows its remaining jobs as the game's abstraction. |
| **Peroxisomes** (Baseline) | Bought from Objective 14's arming | Each hunts the nearest peroxide and absorbs it, losing the **absorb wear**; a peroxide that targets it does the **peroxide damage** instead. At 0 health it wears out and credits its **peroxisome refund**. | Cap counting ordered units. **Auto-replace is not offered** (Level 5). |
| **Spark preview and refusal** (shared) | Any pull | [Shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) | — |
| **Idle watchdog** (shared) | Per the beats table | A non-blocking nudge and its highlight | Shared gaps |

**No tutorial help.** There is no need rule, no floating rescue and no struggle offer. No resource arrives beyond the pools, the glucose ring, the refunds and the wave rewards.

## Tuning

Every number is Tuning. Frame counts and per-frame quantities are Baseline frames at 30 Hz, converted by the rule in *Units*. The rows marked *provisional* hold until playtests confirm them.

| Name | Value | Source | Checked by |
|---|---|---|---|
| Starting resources | ATP 1000, NT 10, AA 100, FA 0, G 50 | Baseline (fork); the walkthrough agrees | Unit test |
| Starting units | 1 mitochondrion (no actions); 25 live ribosomes and 50 live slicers, no pending production, each counted once toward its cap | Baseline | Unit test |
| Membrane | 15 nodes; 100 HP max; max 25 nodes; +10 max HP per node | Code default (D10); Baseline §2.2–2.3 | Unit test |
| Dish radius | 5,000 px | Baseline (fork boundary) | Unit test |
| Edge clamp | Centrosome ≤ dish radius − cell radius, recomputed as the cell grows | As Level 2; provisional | Boundary invariant |
| Starting zoom | Scale 0.2 | Baseline | — |
| Fat zoom | Scale 0.6 over 30 frames, then a scroll to the centrosome | Baseline | Camera ticket |
| Zoom back | Scale 0.2 over 30 frames | Baseline | Camera ticket |
| Vesicle zoom | Scale 0.6 over 15 frames, then a scroll to the nucleus | Baseline | Camera ticket |
| Vesicle zoom-out | Scale 0.2 over 15 frames, then a scroll to the centrosome | Baseline | Camera ticket |
| Mystery zoom | Scale 0.6 over 15 frames, then a scroll to the mitochondrion | Baseline | Camera ticket |
| Wave zoom-out | As Level 2 | Baseline | Camera ticket |
| Pools | G 300 at 0.2; AA 50 at 0.1; both from the recycle gate. No NT or FA pool. | Baseline (the fork declares NT and FA pools that never activate; omitted) | Resource-acquisition runs |
| Regrowth interval; roll distance | 16 frames; 72 px | Baseline effective timing (D16) | Unit test; resource-acquisition runs |
| Gem values | As Level 2 | Baseline | Unit test |
| Glucose ring gems; glucose ring radius | 20 gems of 15 G; 1.05 × lens | Baseline (D17 fixed) | Unit test; resource-acquisition runs |
| Return distance | 2 × lens | Baseline | Unit test |
| Recycle charge; recycle animation | Slicer: 3 ATP charged, 10 AA credited; about 10 frames | Baseline §1.4 (the animation length is medium confidence) | Unit test |
| Fat threshold; surplus amount; surplus interval | G ≥ 54 (the cap 50 plus 4); 4 G into 1 FA; 61 frames | Baseline | Unit test |
| Membrane growth cost | 100 ATP, 50 AA, 5 FA, plus 1 NT tied up in RNA until it breaks down | Baseline §1.4 | Unit test |
| Shrink charge; shrink refund | 50 ATP, checked first (D3 fixed); 50 AA and 5 FA | Baseline §2.2 | Unit test |
| Shrink cooldown | 31 frames | Baseline (§I) | Unit test |
| Move cost | Mitochondrion: 50 ATP per 500 px, charged continuously | Baseline §1.4 | Unit test |
| Burst count; burst delay | 5 peroxide aimed at the nucleus; 25 frames after the Something's happening hold closes | Baseline | Unit test |
| Mystery delay | 45 frames after the burst first exists | Baseline (the fork Objective's delay) | Unit test |
| Reveal delay | 30 frames after the recorded hit, on the gameplay clock | Baseline (the fork Objective's delay) | Unit test |
| Peroxide rate | 1 peroxide per 2 production cycles (0.5 per cycle, accumulated); none on the first cycle after production switches on | Baseline §1.3 | Unit test, after the timing ticket |
| Targeting weights | Mitochondrion 10, peroxisome 1, slicer 0.5, each × count; the nucleus gets ⅓ of that sum; the total × 1.05, the remainder being "no target" (back to the maker). The nucleus's share is always 1 ÷ 4.2 ≈ 23.8%. | Baseline §6, §I | Statistical unit test |
| Peroxide damage | 5 per hit | Baseline | Unit test |
| Peroxide speed | 2.5 px per frame, easing by 1/30 px per frame to 2 px per frame | Baseline (§I) | Distance-over-30-frames unit test |
| Nucleus | 200 HP | Baseline | Unit test |
| Mitochondrion health; stop threshold | 100 HP; stops producing below 50 | Baseline §3.4 | Unit test |
| Repair heal; repair jobs; enzyme refund | 5 per job; **5 jobs per enzyme**, shared and **experimental** ([shared Tuning](shared-defaults.md#tuning)); 100 AA when it wears out | Baseline heal and refund; **jobs are a Deviation** | Unit test; the infester comparison before the value is final |
| Job interval | The shared value (60 frames); not overridden | [Shared Tuning](shared-defaults.md#tuning); provisional | Unit test; script 10; the infester comparison |
| DNA repair cost | 50 ATP, 100 AA, plus 2 NT tied up in RNA; no cap (D5) | Baseline §1.4 | Unit test |
| Peroxisome cost; cap | 50 ATP, 50 AA, 2 FA, plus 1 NT tied up in RNA; cap 10, counting ordered units | Baseline §1.4 | Unit test |
| Peroxisome health; absorb wear; peroxisome refund | 20 HP; 1 per absorption (a targeting hit does the peroxide damage); 50 AA and 2 FA when it wears out | Baseline (§I) | Unit test |
| Peroxisome speed | 4 px per frame | Baseline (§I) | Distance-over-30-frames unit test |
| Ribosome cost; cap | As Level 2; the cap is full all level | Baseline | Unit test |
| Slicer cost; cap | As Level 2 (5 ATP, 10 AA, plus 1 NT tied up in RNA; cap 50, counting ordered units) | Baseline | Unit test |
| Waves | Wave 1: 15 injectors. Wave 2: 25 injectors. None vesicle-wrapped. Spawn rings, injector offspring and dormancy as Level 2. | Baseline §5 | Balance Runs |
| Dormant re-attack | As Level 2 (15 dormant ticks of 31 frames) | Baseline | Unit test |
| Wave rewards | Wave 1: 75 AA. Wave 2: 125 AA and 1 NT. No FA. Credited once when the defeated card is acknowledged. | Baseline (rounded; the walkthrough shows wave 2's 1 NT) | Unit test |
| Repair cost; repair interval | As Level 2 (25 ATP per HP; 61 frames) | Baseline | Unit test |
| Metabolism | As Level 2, including the fat backup. **The cycle length is open** ([the timing ticket](../tickets.md#ticket-85)). | Baseline | Unit test, after the ticket |
| Starvation, lingers, nudges, touch | The shared values; **no overrides** | [Shared Tuning](shared-defaults.md#tuning) | — |

## Win and loss

- **Win:** wave 2 defeated while Objective 19 is armed, under the shared Objective rules.
- **Loss Routes.** Each answers possible, impossible or **unknown**; unknown is used wherever impossibility isn't established, as the shared rule allows.
  - **Lysis:** possible while any wave has live virions, viral RNA or a dormant pool, or while the chain of Objectives that sends it can still complete. Before wave 1, nothing damages the membrane (peroxide never targets it).
  - **Nucleus at 0:** possible while peroxide is in flight or a mitochondrion is producing it. **Impossible** before production switches on, if the chain to the damage reveal can't complete (the burst alone does 25 of 200), and **impossible** once every mitochondrion is damaged and no peroxide exists. **Unknown** when an undamaged mitochondrion exists but isn't producing (switched off, or without fuel), since fuel may still arrive and peroxisomes and repair may prevent lethal damage.
  - **Starvation:** the shared rule. Possible whenever ATP is below the Line, or an automatic drain could take it there. The only automatic drain is membrane repair, which runs only after wave damage. Spending by choice, including a move the student ordered, doesn't count.
- **The exhaustion fact** (a diagnostic, never a predicate on its own) is narrowed to one question: **no remaining route can increase ATP.** That is true when every mitochondrion is damaged, or when every undamaged one lacks fuel with no way to get it: G < 1, FA < 1, no cycle under way, no FA still to come (a peroxisome's wear-out refund needs peroxide in flight), no affordable pull that could reach a gem, and no gem within motion reach. A switched-off mitochondrion with fuel is not exhausted: Toggle is free. It is logged for the loss hint ([hint rules](../tickets.md#ticket-80)) and for Balance Run reports. Whether the resources already held can still finish a task is each predicate's own separate check.

### Declared predicate: *No peroxide source*

True when **all** of these hold:
1. Objective 17 hasn't completed **and still needs at least one absorption**: it is not yet reached, or it is armed with fewer than 3 absorptions counted. (Once the third absorption is counted, the Objective completes after its minimum beat; that window is never win-impossible.) Peroxide production has switched on;
2. every mitochondrion is damaged: below the stop threshold, which nothing in Level 3 can restore or replace (no heal, recycle, divide or new mitochondrion);
3. **no peroxide exists** (in flight or on its way back to its maker). None can be made, by 2.

- **Why it is irreversible:** Objective 17 needs 3 absorptions while armed; absorptions need peroxide; only a working mitochondrion makes peroxide in Level 3; and none can work again. Waves are sent only through Objectives 17 → 18, so the win is out of reach.
- **Not true for:** a mitochondrion that is only switched off or without fuel; peroxide still in flight; Objective 17 armed with its 3 absorptions already counted, waiting for its minimum beat; a damaged mitochondrion after Objective 17 has completed (the student may still win on banked ATP).
- **Reason line** (working text): "Peroxide damaged your mitochondrion, so it can't make ATP or more peroxide."
- **Classification** is the shared rule's, not this spec's: lysis is impossible (the waves can't be sent), the nucleus route is impossible (no peroxide, and none can be made). So:
  - **ATP at or above the Starvation Line, membrane at full health:** Starvation is impossible too, so it is a **Dead End** ("This attempt can't continue").
  - **ATP below the Line:** Starvation is running, so it's a Restart offer under the current Objective, and Starvation ends the attempt.
- **Reachable example:** a student stays active but stuck during Objective 10 (they never manage to order a move) while production runs, and the mitochondrion is damaged. The predicate fires on the tick the last peroxide is gone, with several hundred ATP and a full membrane: a Dead End, shown during Objective 10. The build demonstrates this example.

### Candidates, to validate in the build

These are recorded honestly as **candidates**, not declared, because analysis alone hasn't ruled out every recovery route. [Validate Level 3's win-impossible predicates and Dead End classification in the build](../tickets.md#ticket-89) either finds a reachable example that shows the shortfall and rules out recovery (then declares it, with a regression test) or drops it.

| Candidate | The claim | What must be ruled out | Reason line (working) |
|---|---|---|---|
| **W1 Can't finish recycling** | Before the gate: no route can increase ATP, and ATP is below the recycle charge × the slicers still to recycle | Any ATP route. Before the gate the pools are off and peroxide is off, so the nucleus route is impossible; with ATP ≥ 10 it would classify as a Dead End. | "Not enough ATP left to recycle the rest of your slicers." |
| **W2 Can't finish building up** | After the gate, before wave 1 is sent: no route can increase ATP, and what Objectives 3–17 still need (growths, the shrink charge, the first repair, peroxisomes, at per-unit costs beyond units present and in progress; or FA still needed) can't be met from what is held | Every FA route: stored glucose converting, peroxisome wear-out refunds, glucose still collectable. The nucleus route while peroxide exists. | "No ATP to build what's still needed, and no fuel left to burn." A variant for a damaged producer: "Your mitochondrion is damaged, and there's not enough ATP left to build what's still needed." |
| **W3** | Declared above | — | — |
| **W4 No defence left** | A wave has live or dormant viruses or viral RNA; no slicer is present or ordered; no route can increase ATP; ATP is below a slicer's cost | Any slicer already ordered, any ATP route. Always a Restart offer (the wave keeps damaging the membrane), never a Dead End. It explains a loss already approaching (ATP is below the Line). | "No slicers left, and no ATP to make more." |

- **Dead End status:** possible through *No peroxide source*. The candidates may add more; until the validation task settles them, none is claimed.
- **Results screen:** "Level complete!", the Level Rating from the two Wave Ratings, and the play time ([shared defaults: Clocks](shared-defaults.md#clocks-and-the-results-screen)). No best time.

## Verification

### Time targets

| Measure | Clock | Population | Target | Checked by |
|---|---|---|---|---|
| **In-level time** | From the first Story Hold to "Level complete!", in real time | A typical student (±1 SD) | About **12 min** (working range about 11–14): about 4 min of play and about 8 min of teaching. Provisional planning estimates. | Human playtests |
| Teaching budget | Story Holds: reading, watching demonstrations, learning controls | A typical student | About 8 min across 24 holds | Human playtests; the scripts ticket writes to it |
| Stuck points | Where students stall, and whether they recover | All playtesters | Recorded, not targeted: the Baseline's difficulty spike (teacher observation) | Human playtests; the Session Log |
| **Bot regression band** | Play time | The competent autopilot | 150–360 s, provisional and **reporting-only** until calibrated. The wave re-attack is conditional, as in Level 2. | Balance Runs |

- **The estimate guides the classroom budget; it is not a requirement to make Level 3 faster.** The walkthrough (11:15 in the level, 4:01 of play) is a practised player's, not a typical student's.
- **If playtests exceed about 14 min,** investigate first. Trim the seven-card vesicle sequence if the explanation proves redundant (one on-rails demonstration carries the Foreshadowed content); fix confusing interactions if those are the delay. Keep the economy and the essential demonstrations.
- **The class question** stays the practical one: can a representative group reach the Class Minimum within the actual 70-minute period (`docs/levels.md`)? The running estimate (about 38 of 45 min) is arithmetic on unvalidated numbers.

### Resource-band table (required)

"Reach" is the cheapest pull whose membrane touches the nearest glucose gem.

| State | What moves the student out, or ends the level |
|---|---|
| Before the gate (pools off) | 1000 ATP and 50 G burning; the only drains are recycling and pulls by choice, and pulls find nothing. Out by finishing the recycling, which opens the pools. Spent down: candidate W1. |
| ATP below the Starvation Line, mitochondrion switched on and working, G ≥ 1 or a cycle under way | The mitochondrion credits ATP within one cycle. The warning may show briefly ([the timing ticket](../tickets.md#ticket-85)). |
| ATP below the Line, G = 0, FA ≥ 1, mitochondrion working | The fat backup produces the fat-backup yield per FA. |
| ATP below the Line, mitochondrion **switched off** (from Objective 11), with fuel | Switch it back on (Toggle is free); advice "Check whether a mitochondrion is switched off" once introduced. During Objectives 11–14 the level asked for it off: switching it on early is allowed, and Objective 15 then completes after its introduction. Peroxide resumes. |
| From the cheapest pull up to the Line, G = 0, FA = 0, mitochondrion working | Starvation is running. The spark shows how far the ATP reaches; pulls roll for glucose. Out by finding glucose, or **lost to Starvation**. No rescue. |
| **Mitochondrion damaged** (any ATP) | No ATP income for the rest of the level; glucose is useless (no glycolysis while a mitochondrion exists). Banked ATP finishes what it can. Before Objective 17 completes, once no peroxide is left: **No peroxide source**. After it: the waves on banked ATP, or Starvation once ATP falls below the Line, or lysis. |
| No route can increase ATP, before wave 1 | *No peroxide source* if its conditions hold; otherwise candidates W1 or W2, or a passive pickup, a refund or paid work carries the student on. |
| No route can increase ATP, during a wave | **Winning with existing slicers**, Starvation, or lysis; candidate W4 when no slicer is left or ordered. |
| From the Line up to reach, G = 0 | Short pulls inside the spark roll for gems, or ATP falls into the rows above: a legitimate route to a loss. |
| ATP ≥ reach, G = 0 | Collect glucose. The pools regrow. |
| ATP at or above the Line, G > 0, mitochondrion working | Metabolism. Glucose at or above the fat threshold banks fat. |
| FA short (Objective 4, membrane, peroxisomes) | Collect glucose past the fat threshold; a worn-out peroxisome's refund. |
| NT short for an order (a closed budget until wave 2's reward) | Wait for RNA to break down; the honest temporary-cost refusal (Action Panel contract). |
| AA short for an order | The AA pool, recycling refunds, wear-out refunds, wave rewards; a partial fill buys what can be paid for (D18). |
| ATP short partway through a group recycle | The units already recycled stay recycled; the rest are reported honestly (group selection contract). |
| Take Membrane with ATP below the shrink charge | Refused; no node removed (D3 fixed). |
| Nucleus damaged, ATP ≥ a DNA repair enzyme | Buy repair: each enzyme heals several times (reuse Deviation). Nucleus at 0: **lost**. |
| Membrane damaged, ATP ≥ the repair cost | Repair spends ATP and can push it below the Line. Below the repair cost, repair pauses, and further escapes risk **lysis**. |
| A wave under way, no more slicers affordable | Existing slicers may be enough. If they aren't, escapes double and the attempt ends in **lysis or Starvation**. |
| At the dish's edge | Inward pulls work; a roll outside the dish makes no gem and costs the pool nothing (D15). |

### Balance Runs

**The autopilot's declared outcome:** a **win**, with the Level Rating calculated correctly from its Wave Ratings. Its time and rating are **reported**, not asserted, until calibrated; an unexpected rating is never by itself a reason to rebalance.

Each run separates what it **asserts** from what it **reports**:

1. **Competent.** Asserts the win and a correct rating calculation. Reports play time, rating, and slicers alive when wave 1's first injector lands.
2. **Unprepared.** Builds no slicers until the Incoming hold closes. On fixed seeds, reports the outcome (a win and its rating, lysis or Starvation); asserts only that no stuck-state flag is raised.
3. **Prepared.** Builds slicers during Objectives 3–17. Reports as script 2.
4. **No peroxide source.** Reaches the example (no move ordered during Objective 10 until the mitochondrion is damaged). Asserts the predicate on the tick the last peroxide is gone, the reason line, and a Dead End at ATP ≥ 10; a variant below the Line asserts the Restart offer under the current Objective, then the Starvation loss.
5. **Recovery fixtures for *No peroxide source*.** Each asserts the predicate stays false:

   | Fixture | State | Also asserts |
   |---|---|---|
   | **P1** | Objective 17; the mitochondrion **switched off**, undamaged, with fuel | Switching it on brings peroxide; the autopilot wins |
   | **P2** | Objective 17; the mitochondrion damaged; **peroxide still in flight** that a peroxisome will absorb | The absorption counts; the predicate is evaluated again when none is left |
   | **P3** | Objective 18; the mitochondrion damaged **after** Objective 17 completed; enough banked ATP | The autopilot wins on banked ATP |
   | **P4** | Objective 15; the mitochondrion one hit above the stop threshold | The predicate stays false until the hit that damages it |
   | **P5** | Objective 17 armed; the **third absorption counted** by the last peroxide, which also leaves the mitochondrion damaged (a targeting hit lands the same tick); no peroxide left | The predicate stays false through the minimum beat, Objective 17 completes, and wave 1 is sent |

6. **The validation task's candidates** (W1, W2, W4) run under [their ticket](../tickets.md#ticket-89), not here.
7. **The beat order.** Asserts: the burst appears the burst delay after its hold closes; the holds present in order; the reveal opens on the **recorded hit** whether the hit landed before the What was that? hold opened or after it closed; no burst peroxide moves while a hold is open; production and Move come only after the reveal closes; no stall on any seed.
8. **Group recycling.** With ATP short partway, asserts the units already recycled stay recycled, the report (requested, recycled, reason), and that Objective 2 completes only when no slicer is left and every refund is credited.
9. **The membrane (D3, D24).** Asserts: a shrink at ATP 49 is refused with no node removed; a paid shrink completes Objective 9; ordering growth past 25 nodes counting paid growth in progress is refused. **Reports** the balance effect of D24 (ATP no longer lost to overbuying).
10. **Reusable DNA repair.** Reports the Level 3 comparison with single-use enzymes (ATP spent on repair, nucleus health through the level). The infester comparison for Levels 6–8 is theirs.
11. **Resource acquisition.** A seeded play-through that gathers the glucose ring and the pools, reaches FA 10, grows 3 times and shrinks once, checking the ring spacing and the D16 timings against the Baseline, and reporting how the lens growth changes the spawn chance.
12. **Peroxide targeting.** Headless and statistical: with one mitochondrion and 0–3 peroxisomes, the hit shares match the targeting weights (the nucleus about 23.8%), including the maker fallback.
13. **The dish's edge.** Asserts no pool debit for a roll with no room (D15).

The stuck-state search runs on every Level 3 sweep ([`levels.md`: Testing](../levels.md#testing)).

### Unit tests (core)

- Objective 1: not met with some slicers unselected; met when every remaining slicer is selected, by the action or the gesture; selecting never recycles
- Objective 2: not met while a refund is pending; met when the last refund is credited
- the recycle charge taken at the command and the AA credited when the animation ends; a group run stopping when ATP runs short
- the gate: pools and the ring switch on when the Supplies hold closes, not on completion
- the fat hold at G 53/54; Objective 3 at FA 0/1; Objective 4 at FA 9/10
- Objectives 7 and 8 at nodes 15/16 and 17/18, including three vesicles fusing during a linger
- the node cap counting paid growth in progress (D24); a shrink refused below the shrink charge with no node removed (D3); the shrink cooldown refused honestly; current HP lowered on a shrink only when above the new max
- each action refused before its Objective is armed, including during the previous Objective's completion linger (Recycle, Make Membrane, Take Membrane, Move, Toggle, Buy DNA Repair, Buy Peroxisome)
- Objective 10 met on an accepted move command, not on pressing Move; the move cost charged continuously and the move stopping when ATP runs out
- Objectives 11 and 15 as states, including an early switch-on completing 15 after its introduction and minimum beat
- the burst: its count, delay and aim; exempt from the first-cycle rule; the recorded hit kept across holds; only the burst's hit (not other damage) opening the reveal; the reveal delay on the gameplay clock
- peroxide production off until the reveal closes; none on the first cycle; 1 per 2 cycles; none while switched off or damaged
- targeting: the maker fallback; 5 damage; peroxide never expires; never targets the membrane, ER or ribosomes
- the mitochondrion stops at the threshold (health 50 works, below 50 stops) and nothing restores it; no glycolysis while it exists
- DNA repair: waits while the nucleus is full; heals 5 per job; the next job the job interval later while damage remains, none while full, and a new trip when damage returns; does the repair jobs, then wears out and credits 100 AA once; clears infestation first
- peroxisomes: absorb wear 1, a targeting hit 5; wear-out credits 50 AA and 2 FA once; no auto-replace
- Objective 17 counts only absorptions while armed
- the predicate: each condition just true and just false, including Objective 17 armed at 2 and 3 absorptions counted; stays true once true; its classification at ATP 9 and 10 with a full membrane
- the nucleus Loss Route's three answers, including unknown with an undamaged mitochondrion switched off
- Starvation advice lines appearing only after their teaching beats are acknowledged
- the ways-to-die card at its beat, separate from the once-per-save Starvation card
- the unit conversion: peroxide and peroxisome distances over 30 Baseline frames equal the Baseline's
- the wave rewards credited exactly once, on acknowledgement

### Defect Patches

| Defect | Which test must fail |
|---|---|
| Objective 1 met by selecting one slicer | The Objective 1 unit test |
| Objective 2 met before the last refund | The Objective 2 unit test |
| The pools opening on Objective 2's completion instead of the hold's close | The gate unit test |
| Growth counted by fusion events (a fusion during a linger lost) | The Objective 7/8 linger unit test |
| Shrinking before paying (D3 undone) | Script 9 and the D3 unit test |
| The node cap ignoring growth in progress (D24 undone) | Script 9 |
| The damage reveal keyed to "health below maximum" | The recorded-hit unit test (other damage before the burst's hit must not open the reveal) |
| The reveal keyed to a one-shot event while unarmed (the Baseline's stall) | Script 7 |
| Peroxide speed not converted to 60 Hz (twice as fast) | The unit-conversion test |
| A damaged mitochondrion still producing, or glycolysis starting while it exists | The producer-damage unit test |
| DNA repair enzymes used up after one job | The DNA repair unit test |
| The predicate true with the mitochondrion only switched off | P1 |
| The predicate ignoring peroxide in flight | P2 |
| The predicate firing after Objective 17 completed | P3 |
| The predicate ignoring absorptions already counted | P5 |
| Move enabled from the damage reveal's linger | The action-refusal unit test |
| Advice about switching off shown before its beat | The advice unit test |

## Appendix

### Deviations

| Deviation | Pedagogy Rationale |
|---|---|
| **A visible "Select all slicers" action, and Objective 1 requires the whole group** (Baseline: a hidden drag gesture, and an Objective met by selecting one slicer). The strategic cost (recycle all 50, the charges and refunds) is unchanged. | The teacher has watched many students struggle with the obscure gesture. The lesson is recycling, not finding a control; the student must actually do the group selection rather than watch it. |
| **Reusable DNA repair enzymes:** each does several repair jobs, then wears out and returns its amino acids (Baseline: one heal, then recycled). One shared value, experimental. | Enzymes work repeatedly, a core biology idea; single-use repair taught the opposite. Real cells also break proteins down and replace them, so "works many times, then wears out" is accurate; the number of jobs is the game's abstraction. The teacher's classroom evidence says Level 3's difficulty doesn't depend on it. Its effect on Levels 6–8 is an explicit acceptance question for those specs. |
| **D3 fixed:** the shrink charge is checked before a node is removed. | Recycling that destroys building blocks for nothing contradicts the lesson that recycling returns building blocks, not the ATP spent. |
| **D24 fixed:** the node cap counts paid growth in progress (a behaviour change, checked in script 9). | An overbuy that silently loses 100 ATP teaches nothing; caps count ordered units everywhere else (Level 2). |
| **The placeholder Objectives become holds in an armed gap,** and the damage reveal keys to the burst's recorded hit. The burst's size, damage and the point where production begins are unchanged. | The mystery reads as story instead of a blank or "???" Objective, and a missed event can no longer stall the level. |
| **State predicates** for Objectives 7, 8, 11 and 15, one Objective for the move, and one counted Objective for the three absorptions (Baseline: chained event Objectives). | Work that lands during a completion linger isn't lost, and the student sees one task where the Baseline showed several. |
| **Actions enabled when their Objective is armed** (Baseline: at the preceding tutorial's close). | The same completion-linger fix as Level 2's purchases. |
| **The Baseline's per-Objective delays are replaced by the completion linger,** except the three that time a demonstration. | One shared pacing rule; the demonstrations keep their Baseline timing. |

The Deviations shared by every level also apply, including **hydrogen peroxide** in place of "free radicals" and **Nucleotides (NT)** in place of "NA" (`docs/levels.md`).

### Fork defects this level touches

| Defect | Decision |
|---|---|
| **D1:** NT, AA and FA caps never apply | **Kept uncapped** (Baseline as played) |
| **D2:** 100% recycling refunds | **Kept.** The encyclopedia never states the refund (accuracy rule 5). |
| **D3:** shrinking before paying | **Fixed** (Deviations) |
| **D5:** no cap on DNA repair | **Kept.** Enzymes idle while the nucleus is full, so an overbuy spends ATP and ties up materials until use. |
| **D15:** the pool loses a gem when there's no room | Fixed core-wide (Level 1). Checked in script 13. |
| **D16 family:** off-by-one counters | The effective timings kept as Tuning. Checked in script 11. |
| **D17:** stacked ring gems | **Fixed** for the glucose ring |
| **D18:** partial batches | Kept, honestly reported, for purchases and for group recycling |
| **D19:** nucleolus pores never returned | **Unreachable in Level 3:** ribosomes start at the cap, and players can't recycle ribosomes in any level (§I). Untouched. |
| **D20 (new):** the "no nucleolus pore" refusal is silent | **Fixed core-wide:** an honest refusal (a clear failure, not a Deviation). Not reachable in Level 3. |
| **D21 (new):** the peroxide targeting's lysosome band omits the peroxisome weight | Not reachable (no lysosomes). **Decided in [the Level 4 spec](level-4.md): kept as played**, with a fix recorded as a candidate for a controlled comparison. |
| **D22 (new):** the level file's hygiene (blank and "???" labels, a ghost deactivation, a duplicate id, a label saying "all" for a target of 1) | Not reproduced: our content is written fresh |
| **D23 (new):** worn-out units refund their building blocks | **Kept** for peroxisomes and slicers; DNA repair enzymes refund on wear-out under the reuse Deviation |
| **D24 (new):** the node cap ignores growth in progress | **Fixed** (Deviations), with a Balance check |
| **S1:** the "no working mitochondrion" dialog | Replaced by the shared Restart rule (`docs/levels.md`) |
| **The missed-event stall** (the burst hitting before its Objective is armed) | Fixed by the recorded hit (Beats) |

Untouched: D4, D6–D14.

### Content the fork declares but never uses

Recorded so nobody "restores" it later, thinking it was lost:
- **A third wave of 30 injectors.** Nothing in the level file sends it; the level ends straight after the 25-wave. Not declared in our content.
- **NT and FA pools** (NT 50 at 0.05; FA 250 at 0.3), never switched on. Not declared.
- **The Golgi**, hidden all level. Not placed.

### Proofs

- **The supply gate doesn't strand a student who follows the level:** a student who hasn't spent ATP on pulls (which find nothing before the gate) reaches it with at least 850 ATP (1000 − 150), and nothing else drains ATP before it.
- **The nucleus can't reach 0 before production starts:** the burst does 25 of 200.
- ***No peroxide source* is irreversible:** see its section. The Dead End it produces at ATP ≥ 10 follows from the shared rule, since all three Loss Routes answer impossible.

### Open items carried forward

- **The candidates W1, W2 and W4, and the Loss Route unknowns,** in [the validation task](../tickets.md#ticket-89).
- **The repair jobs value** (5, experimental): the infester comparison is an acceptance question on [Specify Level 6 — Journey](../tickets.md#ticket-67), [Level 7 — Cold](../tickets.md#ticket-68) and [Level 8 — Toxin](../tickets.md#ticket-69).
- **Whether membrane resizing earns its time:** [its evaluation ticket](../tickets.md#ticket-90), on the playable build. It blocks nothing.
- **The metabolism cycle:** [the timing ticket](../tickets.md#ticket-85).
- **The membrane vesicle's route.** The fork sends membrane vesicles ER → membrane in every level, even once the Golgi exists; the Vesicle hold's line explains the shortcut. [Specify Level 4 — Golgi and lysosomes](../tickets.md#ticket-65) pays the line off when the Golgi arrives. Changing the actual route is optional there, and would need its own justification; it doesn't follow automatically from correcting the explanation.

### Sources

- Fork slot 2's level file and source, read for this spec (clean room; nothing copied). The corrections found are recorded in `docs/research/baseline-mechanics.md` §I.
- Walkthrough `docs/baseline/walkthrough/level-3.md`. Its membrane and peroxisome costs look like misreads (the cross-check there); the fork rules on numbers.
- Biology wording follows `docs/learning-targets.md`: lipids are made from fatty acids; fat is the longer-term store; peroxisomes break down hydrogen peroxide; DNA repair proteins recognise and mend damaged DNA, and one repair unit stands for that teamwork; enzymes work repeatedly and cells eventually replace them.
