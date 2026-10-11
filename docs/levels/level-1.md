# Level 1 — Pseudopod (working title)

Players see the title **Wiggle Room** (`docs/story.md`). This file is the full spec:

- decided with the teacher in [Specify Level 1 — Pseudopod](../tickets.md#ticket-62)
- revised after playtesting a throwaway prototype in [Prototype Level 1 from its spec on the Bake-off build](../tickets.md#ticket-77)

It follows [`spec-template.md`](spec-template.md): the build contract comes first and the rationale is in the appendix. The rules for every level are in [`docs/levels.md`](../levels.md), and terms follow `CONTEXT.md`.

## Summary

| | |
|---|---|
| Tier | Required |
| Teaches | **LT1:** glucose is fuel for ATP, and ATP is the currency. **LT2:** the centrosome and cytoskeleton organise movement; the mitochondrion's job. |
| Objectives | 6, as in the Baseline |
| Waves | None |
| Won | ATP ≥ 1000, once Objective 6 is armed |
| Lost | **Never.** Level 1 has no Loss Route (a Deviation) |
| Dead End | None (see the resource-band table and the appendix) |
| Level Rating | None: the results screen shows "Complete!" and the time |
| In-level time | About **3½ min** for a typical student: about 120 s of play plus 90 s of reading. Checked by playtest. |

## Shared infrastructure used

This section links to the shared systems; don't rebuild any of them for this level. The list comes from [`infrastructure-index.md`](infrastructure-index.md), and the shared numbers and UI rules are in [`shared-defaults.md`](shared-defaults.md).

| Shared system | Where | Level 1 specifics |
|---|---|---|
| Game Shell state machine (menus, Pause, a hidden tab, level start and end) | `architecture.md`: Game Shell Pause exists from the first frame; the HUD hold only introduces it. Restart is a fresh attempt with familiar holds quick to pass (Continue at once, Skip to gameplay) ([shared defaults: Restart](shared-defaults.md#restart)). |
| Objectives, and the minimum beat | `architecture.md`: Simulation Core | 6 Objectives (beats table below). |
| Story Hold | Same | 7 holds (beats table below). The cytoplasm hold has an on-rails demonstration. |
| Game Events | `architecture.md`: Game Events | The Objective predicates read core state; the pseudopod counts read `pseudopod_finish` events. |
| Loss Routes, Starvation | [`levels.md`: shared end-of-level rules](../levels.md) | **Starvation is switched off.** There are no Loss Routes. |
| Win-impossible predicates, Dead End | Same | None are declared (see the appendix). |
| The Restart button | Same | The **struggle offer** is Level 1's one exception (Rules). |
| Idle watchdog | `architecture.md`: Simulation Core | Level 1 triggers and highlights (Rules). |
| Pseudopod spark preview and refusal | [Shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) | Used unchanged. |
| Tuning table | `architecture.md`: Content | This spec's Tuning section. |
| Level Rating | [`levels.md`](../levels.md) | None; results show "Complete". |
| Session Log | [Define the Session Log and save format](../tickets.md#ticket-15) | Records the Objective reached. |

**Extensions needed:** none.

## Starting state

| | |
|---|---|
| Resources | **Starting ATP** (Tuning, a Deviation) and 0 of everything else |
| Units | None. The nucleus, ER and Golgi are hidden. |
| World | A petri dish of **dish radius** (Tuning), with the cell at its centre. **The dish's bands are fixed to the dish, not to the camera**, so the cell visibly crosses them, and the dish's rim is drawn. A pseudopod target is clamped so the centrosome stays within the **edge clamp**. |
| Membrane | 15 nodes, so the lens is about 1,191 px (Level 1's own value; D10) |
| Camera | Zoom 0.2, fixed; the zoom control is hidden |
| HUD | Everything hidden; each element appears at its beat (beats table) |
| Glucose pool | **Pool size and regrowth** (Tuning), **inactive** until Objective 3 completes. The NA, FA and AA pools are never switched on, so none is defined. |
| Producers | Cytoplasm glycolysis runs from the start while the cell has no mitochondrion; it has nothing to burn until the first glucose. The **surplus glucose burn** runs from the start. Fat processing is off. |

## Beats

Every beat is a **Story Hold**. The scripts ticket writes the lines; this table fixes only what each beat covers.

| Story Hold | Objective | Completes when | Switches on | Movement | HUD reveals |
|---|---|---|---|---|---|
| **Start.** Ursa shows off the host candidate; Aqua says it needs to move. The centrosome appears at the cell's centre and pulses. | 1. **Get the centrosome** | The student taps or clicks the centrosome, within the shared touch target | The centrosome's encyclopedia entry; the cytoskeleton is drawn | Locked | The Objective pane (shown from Objective 1) |
| **Pseudopod.** How to press on the membrane, drag and release, with an on-rails demonstration. | 2. **Make a pseudopod** | 1 `pseudopod_finish` while armed (a cancelled one counts, Baseline) | The floating rescue and the struggle offer are armed | **Unlocked when Objective 2 is armed** | The ATP meter, on the **first grab of the membrane** (a Deviation) |
| **HUD.** ATP is the cell's energy currency, and every move costs some. Points out the Objective pane, How to Play (Pause menu) and the encyclopedia button. A candidate line: Ursa's "Tardi-squish". | 3. **Make 2 more pseudopods** (counts 2 → 0) | 2 `pseudopod_finish` while armed | The glucose pool; the scripted glucose ring | Free | The encyclopedia button (the Pause button exists from the first frame; this hold introduces it) |
| **Fuel.** Aqua: the cell can move, but it needs energy to keep going. | 4. **Find some fuel** | Glucose held ≥ 1 | The need rule is armed (when Objective 4 is **armed**); the mitochondrion vesicle is spawned (when Objective 4 **completes**) | Free | The glucose meter, when Objective 4 completes |
| **Cytoplasm.** The cytoplasm breaks 1 glucose down into 2 ATP, shown on rails. | 5. **Find a more efficient way to make ATP** | A mitochondrion is in the cell | The mitochondrion is placed; cytoplasm glycolysis stops (Baseline) | Free | — |
| **Mitochondrion.** A mitochondrion gets far more ATP from each glucose: 38, with the disclaimer that rules 1–2 in `docs/learning-targets.md` require. Ursa: "Tardi-Organelles!", then Aqua's correction. The mitochondrion's encyclopedia entry unlocks. | 6. **Build up 1000 ATP** | ATP ≥ 1000, checked **every tick** (D13) | The level is won | Locked once Objective 6 completes | — |
| **Win.** Aqua: hardy, but it needs more to survive the journey. **R.Y.E. is introduced**, then the results screen. | — | — | — | Locked | — |

- **When each "Switches on" entry happens:**
  - Objective 2's (the floating rescue and the struggle offer) when it is armed.
  - Objective 3's (the pool and the ring) when it completes (*Scripted spawns*).
  - Objective 4's need rule when it is armed; its vesicle when it completes.
  - Objectives 5 and 6's entries are results of completing them.

  This was settled in [Build slice: Level 1 content and the Balance Runner](../tickets.md#ticket-114) (spec audit P2, teacher 2026-10-05): the rules table's "from Objective 4 armed" is the trigger.
- **Only finishes made while an Objective is armed count toward it.** A pull made during a completion linger doesn't carry over to the next Objective.
- **The first-time Starvation card never plays in Level 1**, because Starvation is off. It appears the first time the warning shows in a later level.
- **A hold freezes the cell mid-move** on the tick it triggers (shared default, [Story Holds](shared-defaults.md#story-holds)).

## Scripted spawns and rules

### Scripted spawns

- **The glucose ring** (Baseline, D17 fixed):
  - **Placement:** **ring gems** (Tuning) are placed at the **ring radius** (1.30 × the lens radius) from the centrosome, outside the visible lens, as the tick of Objective 3's completing finish sees it, at *i* ÷ *n* of a full turn (18 separate spots). They are static.
  - **The intended experience:** moving around reveals scattered glucose. The ring is an implementation detail that gives early exploration a reliable chance of finding food: students shouldn't notice its circular arrangement or learn to follow its circumference.
  - **Accepted imperfection:** a pickup may occasionally come into view as it appears (the cell moving during a linger, say). That isn't a defect, and perfect off-screen placement isn't required.
  - **To check in playthroughs:** whether the ring's pattern is obvious. If it is, that is a design finding to raise before changing placement or balance.
  - **Returns:** a ring gem more than the **return distance** behind the cell goes back into the pool as +1 (Baseline). The trailing half of the ring often does this soon after placement; that is accepted as Baseline.
- **The mitochondrion vesicle** (Baseline):
  - It spawns at the **vesicle spawn distance** at a random angle when Objective 4 completes.
  - It homes on the centrosome at the **vesicle speeds**, retargeting at the **vesicle retarget interval**.
  - Touching it cancels an extending pseudopod.

### Rules

| Rule | Trigger (and when it's armed) | Effect | Cooldown and limits |
|---|---|---|---|
| **Glucose pool** (Baseline) | While active (from Objective 3's completion): one spawn roll per **roll distance** of travel, with chance = pool count × 1,250 ÷ lens radius² | One gem lands just outside the lens, at a random angle | The pool regrows +1 with the **regrowth chance** every **regrowth interval**. **D15:** a roll whose spot is outside the dish makes no gem and costs the pool nothing. |
| **Surplus glucose burn** (Baseline, §1.3) | G above the **glucose cap**, checked every **surplus interval** | Burns the **surplus amount** of G for nothing | Emitted as its own Game Event, never as ATP production. |
| **Need rule** (Level 1 only) | From Objective 4 armed until the win: G = 0, and **no gem of any kind** within the **need radius** of the centrosome | One **rescue gem** at the **need spawn distance**, ahead of the cell's last move if that spot is inside the dish, otherwise on the line toward the dish's centre | The **need cooldown**. It doesn't draw on the pool, and a need gem left behind vanishes without crediting the pool. |
| **Floating rescue gem** (Level 1 only) | From Objective 2 armed until the win: ATP below the **struggle line**, G = 0, **no mitochondrion cycle processing** (its glucose charged, its ATP not yet credited; [catalogue §1.3](../research/baseline-mechanics.md#13-conversion-rates-metabolism)), and no floating gem already in flight | One **rescue gem** at the **float spawn distance**, at a seeded random angle. It homes straight at the centrosome at the **float speed** until the membrane touches it. | One at a time. Processing only defers spawning a new gem: a gem already in flight continues. It doesn't draw on the pool and is never returned to it. |
| **Struggle offer** (Level 1 only; the one exception to the Restart rule) | From Objective 2 armed until the win: ATP below the **struggle line**, **G = 0, and no mitochondrion cycle processing**, so a floating rescue is always on its way | The Restart button appears under the current Objective, with "Glucose is on its way, or you can start over." (working text) | When the trigger turns false, the line goes at once and the "Restart level" button alone stays for the shared **Restart offer hold**, never vanishing mid-press or while focused ([shared defaults: Lingers](shared-defaults.md#lingers)); the dismissal is cancelled if the trigger comes back. No sound, no modal. Restart is also always in the Pause menu. It never says the level can't be won. |
| **Spark preview and refusal** (shared) | Any pseudopod drag | The arrow is the ordinary colour where a release is affordable and grey where it isn't, with a red spark at the far affordability boundary. A release beyond the spark is **refused** (Baseline all-or-nothing), with the shared refusal cues. | Defined in [shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal). **The refusal must read as intentional, never as a bug.** |
| **Idle watchdog** (shared, with Level 1 triggers) | "No progress" for the **first nudge** time. That means: Objective 4 armed with no glucose absorbed; Objective 6 armed with 0 G held and no glucose absorbed; or any other Objective armed without advancing. **A pseudopod counts as progress only on Objectives 2 and 3.** | A short, non-blocking line from Aqua pointing at the Objective. It highlights the nearest gem (Objectives 4 and 6), the vesicle (Objective 5) or the centrosome (Objective 1), with the shared off-screen arrow ([shared defaults](shared-defaults.md#the-idle-watchdog)). Nothing is highlighted on Objectives 2 and 3. | Gaps of the **first nudge**, then the **second nudge**, then every **repeat nudge**. It is not a Story Hold, and nothing pauses. |

**Tutorial help is Level 1 only.** The need rule, the floating rescue gem and the struggle offer give free glucose, or a Restart, beyond the level's recipe. No later level inherits them; from Level 2 on, students "just have to get good". The idle watchdog and the spark preview aren't tutorial help, and they apply everywhere.

## Tuning

Every number is Tuning. The rows marked *provisional* are set until playtests confirm them.

| Name | Value | Source |
|---|---|---|
| Starting ATP | **950** | Deviation (Baseline 1000); see the appendix |
| Camera zoom | **0.4** view px per world px | Deviation (Baseline 0.2); see the appendix. The teacher's and the reviewer's playthroughs at 1366 × 768, 2026-10-07 |
| Dish radius | 5,000 px | Baseline §8.5 |
| Edge clamp | Centrosome ≤ dish radius − cell radius (4,600 px) | Prototype; provisional |
| Pool size, regrowth chance | 200; 0.3 | Baseline |
| Regrowth interval | 16 Baseline frames | Baseline effective timing (D16) |
| Roll distance | 72 px (6 movement ticks) | Baseline effective timing (D16) |
| Glucose cap, surplus amount, surplus interval | 50; 4 G; 61 Baseline frames | Baseline §1.3 (corrected in place from 60 by [movement and economy core](../tickets.md#ticket-111): the counter must pass 60, the D16 off-by-one; Level 2 uses 61 too) |
| Ring gems, ring radius | 18 gems of 5 G; 1.30 × lens | Baseline (D17 fixed) |
| Return distance | 2 × lens | Baseline |
| Vesicle spawn distance | 1.05 × lens | Baseline |
| Vesicle speeds | 10 px per Baseline frame while farther than √1.1 × lens, 1 px once closer | Baseline |
| Vesicle retarget interval | 15 Baseline frames | Baseline |
| Rescue gem value | 5 G | Provisional |
| Need radius | 1.5 × lens | Provisional |
| Need spawn distance | Lens + one gem diameter (the pool's own spawn distance) | Provisional |
| Need cooldown | 10 s | Provisional |
| Float spawn distance | 1.05 × lens | Provisional |
| Float speed | 4 px per Baseline frame | Provisional |
| Struggle line | ATP below 10 (the Starvation Line, reused as a number) | Provisional |
| Restart offer hold, nudge gaps, touch padding and floor | The shared values | [Shared Tuning](shared-defaults.md#tuning) |

## Win and loss

- **Win:** once Objective 6 is armed, the level is won on the first tick with **ATP ≥ 1000** that also satisfies the shared Objective rules (introduction acknowledged, minimum beat passed). It is checked every tick, not only when a resource changes (D13).
- **Loss Routes:** none. Starvation is off; there are no waves, no peroxide, no membrane damage and no nucleus.
- **Win-impossible predicates:** none. The resource-band table shows a way out of every state.
- **Results screen:** "Level complete!", "Complete!", "No waves were scored", and the play time ([shared defaults: Clocks](shared-defaults.md#clocks-and-the-results-screen)). There is no best time. The save and the Recap show "Complete" (`docs/levels.md`, *The Level Rating*).

## Verification

### Time targets

| Measure | Clock | Population | Target | Checked by |
|---|---|---|---|---|
| **In-level time** | From the first Story Hold to "Level complete!" (the opening cutscene not counted), in real time | A typical student (±1 SD) | About **210 s**: about 120 s of play plus about 90 s of reading | Human playtests |
| Reading budget | Story Holds only | A typical reader | About 90 s for all 7 beats (roughly 150–250 words) | Human playtests; the scripts ticket writes to it |
| **Bot regression band** | Play time | The competent autopilot | **40–65 s**, recalibrated on the real core (verified metabolism, D16 timings) from the Level 1 sweep in [Build slice: Level 1 content and the Balance Runner](../tickets.md#ticket-114): the competent pilot won all 50 seeds in 47.4–53.4 s (median 52.2 s). It replaces the prototype's provisional 25–60 s. It catches regressions; it has no teaching meaning. | The PR Gate smoke run (limit 240 s) and the Level 1 sweep |

- **Classroom time** is about 3½ min in the level plus the 60 s opening cutscene, about 4½ min, inside the 5-minute budget in `docs/levels.md`.
- **Prototype evidence:** the teacher's normal run took 78 s of play and 127 s in the level, on placeholder text.

### Resource-band table (required)

"Cost to reach" means the cost of the cheapest pull whose membrane touches the nearest gem.

| State | What moves the student out |
|---|---|
| Before Objective 2 is armed | The cell can't move, so ATP stays at the starting ATP. |
| ATP below the struggle line, G = 0, no cycle processing | The **floating rescue gem** comes in without the cell moving; the struggle offer shows. |
| ATP below the struggle line, G = 0, a mitochondrion cycle processing | Its 38 ATP arrives within 51 frames (about 1.7 s) and lifts ATP above the line, since ATP can't go below 0. Meanwhile no new floating gem spawns and the struggle offer doesn't show; a gem already in flight continues. The need rule is unchanged. |
| ATP below the struggle line, G > 0 | The cytoplasm (2 ATP about every 2 s) or the mitochondrion (38 ATP per Production Cycle of about 3.2 s; its first credit 1.8–3.2 s after it joins) turns it into ATP without the cell moving ([catalogue §1.3](../research/baseline-mechanics.md#13-conversion-rates-metabolism)). |
| From the struggle line up to the cost to reach, G = 0 | **The spark preview** shows how far the ATP reaches. A pull inside the spark always moves the cell (the cheapest travel is about 2.6 ATP for 128 px). Repeated short pulls reach glucose, or bring ATP below the line, where the floating rescue takes over. With no gem within the need radius, the need rule places one. *(The prototype found this band uncovered: its refusals gave no guidance.)* |
| ATP ≥ the cost to reach, G = 0 | The student collects glucose; the pool regrows without limit. |
| Any ATP, G > 0 | Metabolism turns G into ATP. |
| No mitochondrion yet (Objective 5) | The vesicle homes on the centrosome wherever the cell goes, so waiting works. |
| At the dish's edge | Inward moves are always possible, the need rule places gems inside the dish, and the vesicle still homes. Glucose is sparse there (about 44% of spawn rolls fall outside the dish). That is an edge case, kept winnable, not rebalanced. **Tested at the boundary (PR 182):** from a cell at the edge clamp, a pull with any outward component has no travel and is cancelled, including a pull straight at a gem a little outward of the cell. At the positions sampled, gems 50 and 150 px inside the rim, up to 22° either side of the cell, at 3 places on the rim and 2 seeds, each gem was collected by moving along the rim to beside it. ATP was replenished before each pull, so this shows the geometry, not that a resource-limited student can recover. It doesn't establish that every gem 50 px or more inside the rim is collectable. The outermost 50 px is an acknowledged uncertainty: probed gems there were collectable only from a narrow stretch of rim, and in one placement from none. Spawning and rescues are unchanged. The presentation shows when the dish's wall stops a pull (#115). The Playable Level 1 human test checks recovery, and that following the edge gives no food advantage (#116). Rescue eligibility is revisited if either exposes a recovery problem. |

### Balance Runs

**The autopilot's declared outcome:** a **win** inside the bot regression band, with no struggle offer and no Dead End.

Each scripted run asserts a win, which is what proves its route:
1. **Barely competent:** poor aim, short and wasteful pulls, some wandering.
2. **Willfully bad:** rapid cancelling pulls away from glucose until ATP is below the struggle line, then idle. Asserts that the offer appears, the floating gem arrives and is absorbed, and the offer clears after the linger.
3. **Aims past the spark:** from Objective 4, spends down to between the struggle line and the cost to reach, with 0 G, then releases beyond the spark toward the nearest gem. Asserts the refusal cue each time and no ATP spent. It then releases inside the spark and asserts that the cell moves.
4. **The dish's edge:** heads for the rim, then plays normally. Asserts that no gem is lost from the pool (D15).
5. **Runs from the mitochondrion:** moves away from the vesicle for 60 s. Asserts that the vesicle still arrives.
6. **Frugal:** tiny pulls, then waits. Asserts that the win needs glucose turned into ATP (it can't come from the starting ATP).
7. **Rapid cancellations:** from Objective 2, launches a new long pull every few ticks until ATP is below the struggle line, before Objective 4. Asserts that the offer appears, the floating gem arrives, and Objectives 2 and 3 still complete.
8. **Holding glucose below the line:** asserts that the struggle offer does **not** appear while G > 0 or while a mitochondrion cycle is processing.

**Clamp comparison:** sweeps with the lens-clamped cost and the Baseline's unclamped cost are compared on time-to-win, and both numbers are reported.

### Unit tests (core)

- the win check on a tick with no resource change (D13), just below and just at 1000
- 18 distinct ring spots (D17)
- regrowth every 16 frames and a roll every 72 px (D16)
- no pool debit when there's no room (D15)
- the surplus burn: exactly the surplus amount, only above the cap, at its interval, and never reported as ATP
- the need rule: fires, respects the cooldown, never draws on the pool, places inside the dish, and never fires with G > 0
- the floating gem: one at a time, only below the line with 0 G, armed from Objective 2, seeded angle
- **waiting for production** (reachable): ATP below the line, 0 G, a mitochondrion cycle processing. No new floating gem spawns and no struggle offer shows; a gem already in flight keeps homing; when the credit lands, ATP is above the line and neither is due
- **no glucose and no production pending** (reachable): ATP below the line, 0 G, no cycle processing. The floating gem spawns and the struggle offer shows under the existing ATP rule
- the struggle offer: shows below the line with G = 0; not with G > 0; on clearing, the line goes at once and the button after the Restart offer hold; the dismissal is cancelled if the trigger comes back; the button isn't removed while pressed or focused; at 9.99 and 10.00 ATP
- a cancelled pseudopod is charged and counts toward Objectives 2 and 3
- a pseudopod is idle-watchdog progress only on Objectives 2 and 3
- Objective 6 with ATP already ≥ 1000 completes only after the minimum beat
- no Starvation grace or countdown ever starts in Level 1
- the idle nudge's triggers and gaps
- claiming the centrosome by tap
- the ATP meter shown on the first membrane grab, even for a drag that no frame draws
- spark position against the affordable reach, at the boundary; refusal beyond it with no ATP spent

### Defect Patches

| Defect | Which test must fail |
|---|---|
| The win checked only on a resource change | The D13 unit test |
| The floating rescue gem removed | Script 2 (stuck at 0 ATP) |
| Starvation left on in Level 1 | The "no Starvation" unit test, and script 2 |
| The ring spaced at *i* ÷ 17 | The D17 unit test |
| A struggle offer that never clears | Script 2 |
| The floating rescue armed only from Objective 4 | Script 7 (stuck in Objective 3 at 0 ATP) |
| The struggle offer shown while G > 0 | Script 8 |
| A new floating gem or the struggle offer while a mitochondrion cycle is processing | The waiting-for-production unit test |
| A refusal that spends ATP, or a spark that overstates the reach | Script 3 |

The stuck-state search runs on every Level 1 sweep. Level 1 has no Loss Route, so any state with no progress for the Tuning time is a bug in the rescues or the autopilot, never a legitimate wait.

## Appendix

### Deviations

| Deviation | Pedagogy Rationale |
|---|---|
| **Starting ATP 950** (Baseline 1000) | Every student must turn glucose into ATP at least once to reach 1000. "Build up 1000 ATP" (segment 5c; the Baseline's "Produce ATP until you have 1000") also reads naturally only when the student starts below it. |
| **Starvation is off in Level 1** | The tutorial teaches that ATP comes from glucose. Rescuing a stranded cell with glucose repeats that lesson, whereas failing a student in the tutorial teaches only frustration. |
| **The need rule and the floating rescue gem** (Level 1 only) | The tutorial must reliably connect glucose to ATP. A student who can't find fuel, or can't afford to swim to it, learns nothing about either. Both act only when needed, so normal play sees the Baseline economy. |
| **The struggle offer** (Level 1 only, only with 0 G and no cycle processing) | A student who has spent their ATP down should see a clean way to try again, without the game claiming they can't win. Neither it nor a new floating gem appears during an ordinary production wait, so the game never tells a student they are struggling while their ATP is on its way. |
| **The ATP meter appears on the first membrane grab** | Students read ATP as the price of a visible action. Showing the meter as the first cost is previewed ties the cost to the number. |
| **Spark preview and refusal cues** (shared, UI only) | Showing how far the current ATP reaches turns a refusal into the lesson "cost grows with distance". The Baseline's bare refusal read as a bug in playtests. |
| **Camera zoom 0.4** (Baseline 0.2), **with the lens drawn as the field of view** | The Baseline's 0.2 framed a 640 × 480 stage. On today's screens the same number leaves the cell, its centrosome and the glucose small, so students can't read what they are meant to look at. At 0.4 the cell is the subject of the screen. The Baseline's own field of view is kept: the world beyond the lens is dimmed (the dish stays faintly visible for orientation) and glucose beyond it is hidden, fading in across the window's edge. The intended experience is that moving around reveals scattered glucose, as in the Baseline (see "Placement" under the glucose ring above). World distances, costs and rules are unchanged. The dish's bands are drawn at half contrast so they don't compete with the window. |
| **ATP gain popups with the glucose used, and the meter's gain glow** (segment 5d). Each ATP credit floats "+N ATP" up from above the cell, with "N glucose used" as its own smaller label under it; the ATP meter glows green once. Costs float "−N ATP" in red, as the Baseline does. | The Baseline shows a cost but no production, so a student who skips the story boxes never sees the cell making ATP. The gain, with the glucose it used beside it, shows fuel used and ATP made as two things, never one turning into the other, and the cytoplasm's +2 against the whole pathway's +38 per glucose. The first student playtest (2026-10-09) found the popups noticed, but some students wrote that glucose "turned into" ATP; the separate glucose label answers that. The +38 rises from the cell, never from the mitochondrion alone (accuracy rule 1). |
| **The centrosome is claimed by tapping it** | Required by the Objective rule (an Objective can't complete by acknowledging its own introduction). Tapping the organelle teaches where it is, and it works by touch. |

The Deviations shared by every level also apply: the pseudopod cost clamped to the lens, Story Hold, and our own titles.

### Fork defects this level touches

| Defect | Decision |
|---|---|
| **D13:** the win fires only when a resource changes | **Fixed:** checked every tick once armed. |
| **D17:** the ring stacks 2 gems on one spot | **Fixed:** 18 gems at *i* ÷ 18 of a turn. |
| **D16:** off-by-one tick counters | **Keep the Baseline's effective timing** as Tuning. The Bake-off core uses the nominal 15 frames and 60 px, so it spawns and regrows about 6–20% faster. The build corrects it as a deliberate change *after* the golden traces are recorded (`docs/testing.md`), with its own trace update. |
| **D15:** the pool loses a gem when there's no room | **Fixed core-wide:** no gem and no debit. The fixed-pool levels (5, 6 and 7) need a Balance Run check. |

Untouched: D1 (caps that never apply; Level 1 uses only ATP and glucose) and D10 (the 15-node membrane is stated above as Level 1's own value).

### Why the level can't reach a Dead End

The resource-band table above is the proof: every reachable state has a rule that moves the student out, and there are no Loss Routes. With a mitochondrion, each glucose gives 38 ATP and a 5 G gem is worth 190 ATP, so a student who keeps collecting climbs to 1000 under any play. The first version of this proof said only that "some pseudopod is affordable". The prototype showed that a refusal with no guidance could strand a student between the struggle line and the cost to reach. The spark preview closes that band.

### History

- **The rescue idea's origin:** the "just in time" rescue idea came from the teacher, prompted by the Godot attempt; only the idea carried over, not its logic.
- **The prototype evidence:** the prototype, its headless play-throughs, the playtests and the full list of gaps are on the unmerged `prototype/level-1` branch, in `docs/prototypes/level-1-findings.md`.
