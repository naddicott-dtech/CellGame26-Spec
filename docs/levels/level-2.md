# Level 2 — Nucleus (working title)

Players see the title **Gesundheit** (`docs/story.md`). This file is the full spec, decided with the teacher in [Specify Level 2 — Nucleus](../tickets.md#ticket-63).

It follows [`spec-template.md`](spec-template.md): the build contract comes first and the rationale is in the appendix. The rules for every level are in [`docs/levels.md`](../levels.md), the shared numbers and UI rules in [`shared-defaults.md`](shared-defaults.md), and terms follow `CONTEXT.md`. [`level-1.md`](level-1.md) is the worked example.

**Units.** Every frame count here is in **Baseline frames at 30 Hz**, the fork's rate. The core runs at 60 Hz, so 1 Baseline frame is 2 core ticks: 16 Baseline frames are 32 ticks, 61 are 122 ticks, and 15 dormant ticks of 31 frames take 15.5 s. Never mix the two units.

## Summary

| | |
|---|---|
| Tier | Required |
| Teaches | **LT1:** amino acids are chained into proteins (slicers); nucleotides are chained into nucleic acids such as RNA. **LT2:** the nucleus holds DNA and sends out RNA instructions; ribosomes use them to build proteins. **Foreshadowed:** DNA → RNA → ribosome → protein. |
| Objectives | 9 (beats table) |
| Waves | Injectors: 5, then 15 |
| Won | Wave 2 defeated |
| Lost | Membrane at 0 (lysis) or Starvation. The nucleus can't be damaged in this level. |
| Dead End | None identified (see *Win and loss*) |
| Level Rating | The shared rules, from 2 waves weighted 5 and 15 |
| In-level time | About **7½ min** for a typical student (working range about 7–9 min): about 3 min of play and about 4½ min of teaching. Checked by playtest. |

Level 2 is the **first level with no tutorial help** (no rescues, no struggle offer; [shared defaults, teacher rule 2](shared-defaults.md#teacher-rules-for-every-level)) and the **first where Starvation can fire**, so the first-time Starvation card can appear here.

## Shared infrastructure used

This section links to the shared systems; don't rebuild any of them for this level. The list comes from [`infrastructure-index.md`](infrastructure-index.md).

| Shared system | Where | Level 2 specifics |
|---|---|---|
| Game Shell state machine | `architecture.md`: Game Shell | Pause and the encyclopedia exist from the first frame ([shared defaults](shared-defaults.md#pause-how-to-play-and-the-encyclopedia)). |
| Objectives, and the minimum beat | `architecture.md`: Simulation Core; [shared defaults](shared-defaults.md#objectives-completion-and-the-linger) | 9 Objectives. Objective 2 is a two-part checklist; each part records its own discovery (Beats). |
| Story Hold | Same; [shared defaults: Story Holds](shared-defaults.md#story-holds) | 16 holds, 5 of them mid-Objective (beats table). Holds that fire on the same tick queue in the stated order. |
| Game Events | `architecture.md`: Game Events | Purchase results (requested, bought, cost, reason), pickups, unit arrivals and wave events feed Objectives and the watchdog. |
| Loss Routes, Starvation | [`levels.md`: shared end-of-level rules](../levels.md#the-shared-end-of-level-rules) | **Starvation is on**, with the shared values. Lysis is possible while a wave can still act. The nucleus route is impossible throughout. |
| Win-impossible predicates, Dead End | Same | One predicate, *Can't finish building up* (Win and loss). |
| The Restart button | Same; [shared defaults: Restart](shared-defaults.md#restart) | The shared rule. No struggle offer. |
| Idle watchdog | [shared defaults](shared-defaults.md#the-idle-watchdog) | Progress and highlight per Objective (beats table). |
| Spark preview and refusal | [shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) | Used unchanged. It is the only guidance in the Starvation bands. |
| Tuning | `architecture.md`: Content; [shared Tuning](shared-defaults.md#tuning) | This spec's Tuning section. No shared value is overridden. |
| Level Rating | [`levels.md`](../levels.md#the-level-rating) | Waves of 5 and 15. |
| Session Log | [Define the Session Log and save format](../tickets.md#ticket-15) | Also records partial fills and purchase refusals, from the purchase events. |
| The adapters | `architecture.md`: Renderer | No new adapter. |
| Selection and touch targets | [shared defaults](shared-defaults.md#touch-and-selection) | The nucleus, mitochondrion, ribosomes and slicers are selectable. The nucleus is claimed by tapping it. |
| The world edge | [shared defaults](shared-defaults.md#the-world-edge) | Level 2 declares its own geometry and look (Starting state). |
| Left-behind gems | [shared defaults](shared-defaults.md#gems-not-drawn-from-a-pool) | Both scripted rings credit their pool (Scripted spawns). |
| Balance Runs and the stuck-state search | [`levels.md`: Testing](../levels.md#testing) | Verification. |

**Baseline simulation systems used here for the first time.** These need no design ticket, because the catalogue (`docs/research/baseline-mechanics.md`) fixes them. They are shared systems, listed in the infrastructure index and owned by the build, **never implemented privately inside Level 2**:
- the production pipeline: RNA, ribosomes, enzymes (§3.2)
- virus behaviour and waves: injectors, hijacking, escape, dormancy, defeat, rewards (§5)
- membrane health and self-repair (§2.3), shown with the shared health display
- the purchase rules: costs, batches, caps, pores (§1.4, and D18 and D19 below)

**Extensions needed.** Each is a shared interaction contract that finishes an existing part of the architecture. Each preserves the catalogue's gameplay rules and timing, and raises any proposed mechanical change separately. They block the Level 2 **build**, not this spec. If one changes an Objective, a timing or an available player action here, that part of this spec is revisited; a dependency isn't resolved just because it has a ticket.
- [Camera zoom, scroll and scripted moves: the shared interaction contract](../tickets.md#ticket-82)
- [Action Panel purchases: the shared interaction contract](../tickets.md#ticket-83)
- [Virus wave presentation: the shared interaction contract](../tickets.md#ticket-84)

**Open dependency:** [Verify the mitochondrion's production cycle](../tickets.md#ticket-85) (the warning check is [its own ticket](../tickets.md#ticket-134)). The metabolism row of the Tuning table isn't build-complete until it's resolved.

## Starting state

| | |
|---|---|
| Resources | **Starting resources** (Tuning): ATP and glucose only |
| Units | 1 mitochondrion, **selectable but with no actions** (a Deviation; its actions arrive at their own introductions in later levels). The nucleus arrives at beat 1. No ER or Golgi. |
| World | The same lab petri dish as Level 1, **declared again in Level 2's own content**, not inherited: the **dish radius**, world-fixed bands and a drawn rim, with the cell at the centre. Pulls are clamped by the **edge clamp**. |
| Membrane | 15 nodes and 100 HP max (**membrane**, Tuning), so the lens is about 1,191 px |
| Camera | The **starting zoom**, following the cell. The zoom tool is hidden until revealed (beats table); every camera input works before then ([shared defaults: The camera](shared-defaults.md#the-camera)). |
| HUD | The ATP and glucose meters, the Objective pane, Pause and the encyclopedia button from the first frame. The rest appear at their beats. |
| Pools | The NT, AA and glucose pools (**pools**, Tuning) are **inactive until the nucleus beat**. The fatty-acid pool is never switched on. |
| Producers | The mitochondrion burns glucose from the start (**metabolism**, Tuning). Cytoplasm glycolysis doesn't run, because the cell has a mitochondrion. **Fat processing is on** (Baseline): surplus glucose becomes fatty acids. Radicals are off. |
| Movement | Free from the first frame (Baseline). Pulls before the nucleus beat find nothing, because the pools are off. |

## Beats

Every row with a hold is a **Story Hold**. The scripts ticket ([Script the ground trials](../tickets.md#ticket-73)) writes the lines; this table fixes what each beat covers. "Mid" marks a hold that fires inside an armed Objective, on an event.

| Story Hold | Objective | Completes when | Switches on | Movement | HUD reveals | Watchdog progress and highlight |
|---|---|---|---|---|---|---|
| **Start.** The cell needs more organelles, starting with a nucleus (organs ↔ organelles). The nucleus appears as a new thing and pulses. | 1. **Get the nucleus** | The student taps the nucleus, within the shared touch target | The nucleus arrives (about 1.4 s arrival, as the Baseline's) | Free | — | None; the nucleus |
| **Nucleus.** The nucleus holds the DNA, including the hidden Settler genome, so protecting it is the mission (`docs/story.md`). It sends out RNA instructions. The cell needs raw materials: amino acids and nucleotides. | 2. **Collect amino acids and nucleotides** (a checklist: amino acids ☐, nucleotides ☐) | **Both** discoveries are recorded **and** both discovery holds acknowledged | The NT, AA and glucose pools; the **AA ring** and the **NT ring** | Free | — | Each first pickup; the nearest gem of a kind still needed |
| *Mid:* **Amino acids.** The first AA pickup. Amino acids are the building blocks of proteins. | (Objective 2) | — | Records the AA discovery | — | The AA meter | — |
| *Mid:* **Nucleotides.** The first NT pickup. Nucleotides are the building blocks chained into nucleic acids, such as RNA. | (Objective 2) | — | Records the NT discovery | — | The NT meter, labelled "Nucleotides (NT)" | — |
| **How to build.** The cell needs ribosomes to build proteins. Select the nucleus to see what it can do. | 3. **Select the nucleus** | The nucleus is selected | — (no purchase yet: see below) | Free | — | None; the nucleus |
| **Building.** The Action Panel: its name, health, description, encyclopedia link and actions (on rails). | 4. **Make some ribosomes** | A ribosome purchase **orders at least 1 unit** (a partial fill counts, D18) | The Buy Ribosomes action, **when Objective 4 is armed** | Free | — | None; the Buy Ribosomes action |
| **Camera.** Something is happening at the nucleus. **Scripted zoom 1** and a scroll to the nucleus, then how to zoom and scroll (the camera ticket). | 5. **Make ribosomes until you have 20** | Ribosomes **≥ 20** (never exact equality) | — | Free | The zoom control, when Objective 4 completes, before this hold's cards (Baseline) | Each ribosome ordered **or** arriving; Buy Ribosomes, **cleared** once ribosomes in the cell plus those ordered reach the target (the student is waiting for production) |
| *Mid:* **Ribosomes.** When ribosomes first reach **≥ 5**. Ribosomes are the cell's protein factories (made of RNA and protein). On rails: the RNA instructions reach a ribosome, which assembles amino acids into a protein. **The RNA strand never turns into the product.** The general **cost card** becomes available from here (Action Panel ticket). | (Objective 5) | — | — | — | — | — |
| **Slicers.** With plenty of ribosomes, the cell can build simple proteins such as slicers. | 6. **Make some slicers** | A slicer purchase orders at least 1 unit | The Buy Slicers action, **when Objective 6 is armed** | Free | — | None; the Buy Slicers action |
| **The nucleus is making something else.** **Scripted zoom 2** and a scroll to the nucleus. | 7. **Make slicers until you have 10** | Slicers **≥ 10** | — | Free | — | Each slicer ordered or arriving; Buy Slicers, cleared once slicers in the cell plus those ordered reach the target |
| *Mid:* **Slicer discovery.** When slicers first reach **≥ 5**. Slicers are the game's protein defences, which cut viral RNA. The **temporary-cost** teaching becomes available from here (Action Panel ticket): the nucleotides come back when the game's RNA breaks down (game accounting). | (Objective 7) | — | — | — | — | — |
| **Achoo.** R.Y.E. brings in viruses to test toughness, with a mock sneeze (the title's joke). | — (the gap before Objective 8; see below) | — | **Wave 1** is sent when this hold closes | Free | The wave display (wave ticket) | — |
| **Viral defence.** Opens when wave 1's **alert ends** (**alert duration**, Baseline). Viruses invade cells; injectors put viral RNA into the cell; slicers cut viral RNA (on rails). | 8. **Survive the viruses** | Wave 1 defeated | — | Free | The membrane-health gauge, when the alert ends, before this hold's cards (Baseline) | Any drop in the wave's live plus dormant count; Buy Slicers, **suppressed** when the slicer cap blocks another purchase |
| *Mid:* **Escaped virus.** Armed with Objective 8; fires **once**, in either wave, when a wave has **no live virions and no viral RNA but a dormant count above 0**: everything left of it is outside, waiting to re-attack (the Baseline's dormant trigger, not the first escape). It opens after the **escaped-hold delay**. Escaped viruses come back. | (Objective 8 or 9) | — | — | — | — | — |
| **Membrane repair.** After wave 1's defeated card is acknowledged. Viruses damage the membrane on the way out; it repairs itself using ATP. More viruses are coming: the cell needs more slicers. | 9. **Survive the second wave** | Wave 2 defeated | **Wave 2** is sent when this hold closes | Free | — | As Objective 8 |
| **Win.** After wave 2's card. Slicer turnover: each slicer is used up when it cuts, and returns its amino acids. Then the story's close: hardy cells, and R.Y.E.'s "puddles" line (`docs/story.md`). Then the results screen. | — | — | — | Locked | — | — |

- **Objective 2 records each discovery separately and keeps it.** It is not an inventory check at the end: spending the AA later doesn't undo the AA part. Both discovery holds must be acknowledged before Objective 2 completes. If both first pickups land on the same tick, both holds queue, **amino acids first**, and neither is lost.
- **The ≥ 5 milestones trigger once**, at "at least 5", so buying quickly can't skip their teaching.
- **A purchase action is enabled only when its first-purchase Objective is armed** (Buy Ribosomes with Objective 4, Buy Slicers with Objective 6), and the core refuses it before then. Otherwise a purchase made during the previous Objective's completion linger, when play runs, wouldn't count toward the purchase Objective. The fork has no such window, because its tutorial opens the moment the nucleus is selected.
- **Ordered units satisfy a count, not a first purchase.** Objective 5's 20 counts the ribosomes in the cell. The watchdog also looks at orders, but only to clear its highlight.
- **The gap before Objective 8.** Between the Achoo hold and the alert's end (the **alert duration**), no Objective is armed and play runs; the viruses are approaching. This replaces the Baseline's "See if any viruses got in" Objective, which only waited for the alert, without moving the teaching earlier.
- **Wave-defeated cards** come from the wave ticket. Acknowledging one credits its **wave reward** exactly once, in the core, before the next hold opens.
- **The first-time Starvation card** may open at any point where the warning first shows (shared rule). It is once per save.

## Scripted spawns and rules

### Scripted spawns

Both rings are placed when the Nucleus hold opens, around the centrosome's position on that tick, at *i* ÷ *n* of a full turn (D17 fixed).

| Spawn | Placement | Visibility | Left behind |
|---|---|---|---|
| **AA ring** (Baseline) | The **AA ring gems** at the **AA ring radius**, static | Outside the visible lens when placed | Farther than the **return distance** from the centrosome: **credits the AA pool by +1**; the gem's value is lost (Baseline) |
| **NT ring** (Baseline) | The **NT ring gems** at the **NT ring radius**, static | Just outside the lens when placed | Farther than the return distance: **credits the NT pool by +1**; the value is lost (Baseline) |

### Rules

| Rule | Trigger (armed between) | Effect | Cooldown and limits |
|---|---|---|---|
| **Pools** (Baseline) | From the Nucleus hold to the end: a spawn roll per pool every **roll distance** of travel | One gem just outside the lens, at a random angle (shared pool rules, as Level 1) | Regrowth every **regrowth interval**. D15: a roll with no room makes no gem and costs nothing. |
| **Purchases** (Baseline, D18) | Buy Ribosomes from Objective 4's arming; Buy Slicers from Objective 6's arming. The core refuses both before then. | Buys the **batch size** one unit at a time until a unit fails: cap reached, can't afford, or no free pore (ribosomes only). Only the ordered units are charged. The core reports the requested quantity, the bought quantity, the actual cost and the limiting reason; the UI shows it (Action Panel ticket). | Caps count units **ordered** as well as present. The pore check runs for each unit (D19). |
| **Production** (Baseline, §3.2) | Any order | Ribosomes grow from a nucleolus pore; slicers are made through RNA and a free ribosome | The shared pipeline. No timers beyond it. |
| **Slicers** (Baseline) | A live slicer and viral RNA | The slicer hunts and cuts the viral RNA, and is **used up**, refunding its AA | No ATP cost to attack |
| **Waves** (Baseline, §5) | Wave 1 when the Achoo hold closes; wave 2 when the Membrane repair hold closes | The **waves** (Tuning). On a send with no wave active, the camera zooms out (**wave zoom-out**). | Escapees re-attack after the **dormant re-attack** time |
| **Membrane repair** (Baseline, §2.3) | Membrane below max health | Spends the **repair cost** for 1 HP every **repair interval** | Skips silently while ATP is below the repair cost |
| **Surplus glucose to fat** (Baseline) | Glucose at or above the **fat threshold**, checked every **surplus interval** | Converts the **surplus amount** of glucose into 1 FA | Fat processing is on in this level |
| **Mitochondrion** (Baseline) | While it has fuel | Burns glucose for ATP; when G < 1 and ATP is below the **fat-backup ceiling**, it burns 1 FA for the **fat-backup yield** | Its cycle length is [the timing ticket](../tickets.md#ticket-85) |
| **Mitochondrion actions** (Deviation) | Always | None are offered. The core refuses them too, so a hidden action can't run. | Move and Toggle arrive in Level 3; Divide and Recycle at their own teaching beats |
| **Spark preview and refusal** (shared) | Any pull | [Shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) | — |
| **Idle watchdog** (shared) | Per the beats table | A non-blocking nudge and its highlight | Shared gaps |

**No tutorial help.** There is no need rule, no floating rescue and no struggle offer. No resource arrives beyond the pools, the two rings and the wave rewards.

## Tuning

Every number is Tuning. Frame counts are Baseline frames at 30 Hz. The rows marked *provisional* hold until playtests confirm them.

| Name | Value | Source | Checked by |
|---|---|---|---|
| Starting resources | ATP 500, G 25, NT 0, AA 0, FA 0 | Baseline (fork). The walkthrough's 355 was an early reading while the mitochondrion ran. | Unit test |
| Starting units | 1 mitochondrion, with no actions | Fork for the unit; **no actions is a Deviation** (appendix) | Unit test |
| Membrane | 15 nodes; 100 HP max | Code default (D10) | Unit test |
| Dish radius | 5,000 px | Baseline (fork boundary) | Unit test |
| Edge clamp | Centrosome ≤ dish radius − cell radius (4,600 px) | As Level 1; provisional | Boundary invariant |
| Starting zoom | Scale **0.4** (candidate) | Deviation from the Baseline's 0.2, following Level 1's 0.4 (appendix) | The camera pass |
| Scripted zoom 1 | Scale **1.2** (candidate) over 15 frames, then a scroll to the nucleus | Baseline's 0.6 × 2 (appendix); timing Baseline | The camera pass |
| Scripted zoom 2 | Scale **1.6** (candidate) over 45 frames, then a scroll to the nucleus | Baseline's 0.8 × 2 (appendix); timing Baseline | The camera pass |
| Wave zoom-out | To scale **0.4** (candidate) over 30 frames, only when no wave is active and the camera is zoomed in further; the zoom only, not the centre | Baseline's 0.2 × 2 (appendix); timing Baseline | The camera pass |
| Player zoom range; scripted maximum; steps | **0.2–4.2**; scripted zooms clamped to **3.8**; zoom tool ± **0.08** a step, wheel **0.16** a notch (all candidates) | The Baseline's 0.1–2.1, 1.9, 0.04 and 0.08, × 2 (appendix) | The camera pass; the Chromebook control check |
| Pools | NT 50 at 0.05; AA 50 at 0.1; G 50 at 0.2. FA declared, never active. | Baseline | Resource-acquisition runs |
| Regrowth interval; roll distance | 16 frames; 72 px | Baseline effective timing (D16) | Unit test; resource-acquisition runs |
| Gem values | NT 1, AA 50, G 2 base; 70% ×1, 25% ×2, 5% ×4; collision radius 25 px | Baseline | Unit test |
| AA ring gems; AA ring radius | 18 gems of 50 AA; 1.30 × lens | Baseline (D17 fixed) | Unit test; resource-acquisition runs |
| NT ring gems; NT ring radius | 9 gems of 10 NT; 1.05 × lens | Baseline (D17 fixed) | Unit test; resource-acquisition runs |
| Return distance | 2 × lens | Baseline | Unit test |
| Batch size | 5 **requested**; the order may be smaller (D18) | Baseline | Unit test |
| Ribosome cost; cap | 2 ATP, 1 NT (permanent), 10 AA each; nominal cap 25, counting ordered units | Baseline §1.4 | Unit test |
| Slicer cost; cap | 5 ATP, 10 AA each, plus 1 NT tied up in RNA until it breaks down; cap 50, counting ordered units | Baseline §1.4 | Unit test |
| Nucleolus pores | 24. A ribosome purchase needs **more than 1** ready pore at that unit's check; a pore is taken when the ribosome is generated, and a ribosome generated with no ready pore uses a busy pore's spot. This can set a lower effective limit than the nominal cap (D19). | Baseline | Script 7 |
| Milestones | Ribosome hold at ≥ 5; Objective 5 at ≥ 20; slicer hold at ≥ 5; Objective 7 at ≥ 10 | Baseline | Unit test |
| Waves | Wave 1: 5 injectors. Wave 2: 15 injectors. No vesicles. Spawn rings: 7 on the first at 1.2 × lens, each further ring doubling, 0.2 × lens apart, ±75 px jitter. Injector offspring 2. | Baseline §5 | Balance Runs |
| Dormant re-attack | 15 dormant ticks of 31 frames (15.5 s), reset by every escape and every virion birth | Baseline | Unit test |
| Escaped-hold delay | 45 frames after the dormant condition first holds. Once pending, the hold opens even if the condition has since changed. | Baseline (the fork Objective's delay) | Unit test |
| Alert duration | About 60 frames from the send | Baseline (the virus panel's glow) | Unit test |
| Wave rewards | Wave 1: 25 AA. Wave 2: 75 AA. No NT or FA. Credited once when the defeated card is acknowledged. | Baseline | Unit test |
| Repair cost; repair interval | 25 ATP per HP; 61 frames | Baseline (D16 family) | Unit test |
| Metabolism | 38 ATP per G. **Fat-backup yield** 108 ATP per FA, when G < 1 and ATP is below the **fat-backup ceiling** of 250. **Production Cycle:** 97 frames credit to credit (46 counting, then 51 from charge to credit), with a random first cycle ([catalogue §1.3, §L](../research/baseline-mechanics.md#13-conversion-rates-metabolism)). | Baseline | Unit test |
| Fat threshold; surplus amount; surplus interval | G ≥ 54 (the cap 50 plus 4); 4 G into 1 FA; 61 frames | Baseline | Unit test |
| Starvation, lingers, nudges, touch | The shared values; **no overrides** | [Shared Tuning](shared-defaults.md#tuning) | — |


## Win and loss

- **Win:** wave 2 defeated while Objective 9 is armed, under the shared Objective rules.
- **Loss Routes:**
  - **Lysis:** possible while any wave has live virions, viral RNA or a dormant pool, or can still be sent.
  - **Nucleus at 0:** impossible throughout. Nothing in Level 2 damages the nucleus (radicals are off; there are no infesters).
  - **Starvation:** the shared rule. Possible whenever ATP is below the line, or membrane repair could take it there.
- **The exhaustion fact** (a diagnostic, never a predicate on its own): no valid pull is affordable (asked of the pull resolver), G < 1, FA < 1, and no mitochondrion cycle and no pull is under way. It is logged for the loss hint ([hint rules](../tickets.md#ticket-80)) and for Balance Run reports. It never shows Restart by itself: a cell that has spent everything can still win a wave with slicers it already built.
- **Win-impossible predicate: *Can't finish building up*.** True when **all** of these hold:
  1. the exhaustion fact;
  2. wave 1 has not been sent;
  3. **What Objectives 2–7 still need can't be met.** Either a pickup is still needed, or the remaining purchases cost more than can still be available. "Remaining" means units beyond those present and already ordered, at their per-unit costs (partial fills make a single unit purchasable, D18). "Can still be available" means what is held plus the nucleotides tied up in RNA, which come back when it breaks down. Under the exhaustion fact ATP can never rise, so the ATP comparison is final. Ordered units cover a future **count**; they never cover a first-purchase Objective that hasn't happened.
  4. **No passive route remains:** no glucose gem, and no gem of a kind still needed, lies within the membrane's **motion reach** (a conservative core calculation of how far the resting, breathing membrane actually moves, not a visual estimate). If that reach can't be established confidently, the predicate stays false.

  - **Why it is irreversible:** under the exhaustion fact nothing can be reached, and nothing in Level 2 raises ATP without moving: rewards are AA only (and come only after a wave), there are no scripted glucose spawns, and recycling isn't offered. A single cheap purchase can still happen (a 2 ATP ribosome), which is why condition 3 compares the whole remaining cost, not the price of the next unit. Every slicer still needed costs 5 ATP.
  - **It never fires once wave 1 is sent.** From then on, the win needs no payment.
  - **Reason line** (working text): "No ATP to build what's still needed, and no glucose or fat to burn."
  - **Reachable example:** Objective 5 armed with 15 ribosomes, 2 ATP, 0 G, 0 FA and no gem within motion reach. The predicate fires, Restart appears under Objective 5, and Starvation ends the attempt.
- **Dead End status:** No Dead End example has been identified. The declared pre-wave predicate always leaves Starvation possible (under the exhaustion fact ATP is below the cheapest pull, which is below the Starvation Line, and can't rise), so it produces a Restart offer, not a Dead End. Broader stuck-state coverage remains subject to Balance Run validation.
- **Results screen:** "Level complete!", the Level Rating from the two Wave Ratings, and the play time ([shared defaults: Clocks](shared-defaults.md#clocks-and-the-results-screen)). No best time.

## Verification

### Time targets

| Measure | Clock | Population | Target | Checked by |
|---|---|---|---|---|
| **In-level time** | From the first Story Hold to "Level complete!", in real time | A typical student (±1 SD) | About **7½ min** (working range about 7–9 min): about 3 min of play and about 4½ min of teaching | Human playtests |
| Teaching budget | Story Holds: reading, watching demonstrations, learning controls | A typical student | About 4½ min across 16 holds. Word count alone won't predict it. | Human playtests; the scripts ticket writes to it |
| **Bot regression band** | Play time | The competent autopilot | 90–240 s, provisional and **reporting-only** until calibrated from the bots. It has no teaching meaning. The 15.5 s re-attack is conditional (a clean defence never triggers it), so it sets no universal minimum. | Balance Runs |

- **The estimate guides the classroom budget; it is not a requirement to make Level 2 faster.** The walkthrough (6:44 in the level, 1:40 of play) is a reference, not evidence of typical-student speed: unfamiliar purchases and production add hesitation.
- **If playtests exceed about 9 min,** first find where the time went. Trim redundant text or clarify confusing controls before touching production, supplies or waves. Keep the essential demonstrations.
- **The acceptance question is the class one:** can a representative group reach the Class Minimum within the period, with ordinary hesitation and retries (`docs/levels.md`)? The running estimate (Level 1 about 4½, Level 2 about 7½, Level 3 about 12, Level 4 about 7, the Launch cutscene and Level 5 to the chloroplast about 2, one Restart about 5) gives about 38 min against 45. That slack is estimated, not validated, and a late loss costs more than 5 min.

### Resource-band table (required)

"Reach" is the cheapest pull whose membrane touches the nearest glucose gem.

| State | What moves the student out, or ends the level |
|---|---|
| Before the Nucleus hold (pools off) | G turns into ATP, and the only Objective is a tap. Pulls find nothing yet. |
| ATP below the Starvation Line, G ≥ 1 or a cycle under way | The mitochondrion credits ATP within one cycle. The warning may show briefly ([the timing ticket](../tickets.md#ticket-85)). |
| ATP below the line, G = 0, FA ≥ 1 | The fat backup produces the fat-backup yield per FA. |
| From the cheapest pull up to the line, G = 0, FA = 0 | Starvation is running. The spark shows how far the ATP reaches; pulls roll for glucose. Out by finding glucose, or **lost to Starvation**. No rescue. |
| Exhausted (the exhaustion fact), before wave 1 | *Can't finish building up* if its conditions hold: Restart shows, and Starvation ends it. Otherwise a passive pickup or paid work can still carry the student on. |
| Exhausted, during a wave | **Winning with existing defences**, Starvation, or lysis. No predicate. |
| From the line up to reach, G = 0 | Short pulls inside the spark roll for gems, or ATP falls into the rows above: a legitimate route to a loss. |
| ATP ≥ reach, G = 0 | Collect glucose. The pools regrow. |
| ATP at or above the line, G > 0 | Metabolism. Glucose at or above the fat threshold banks fat for later. |
| NT or AA short for a purchase | Collect from the rings and pools; a partial fill buys what can be paid for (D18). |
| Membrane damaged, ATP ≥ the repair cost | Repair spends ATP and can push it below the line. Below the repair cost, repair pauses, and further escapes risk **lysis**. |
| A wave under way, no more slicers affordable | Existing slicers may be enough. If they aren't, escapes double and the attempt ends in **lysis or Starvation**. |
| At the dish's edge | Inward pulls work; a roll outside the dish makes no gem and costs the pool nothing (D15). |

### The camera pass and the Chromebook control check

The camera numbers are **candidates** until these checks pass. The results are recorded as Tuning, with any Deviation, in this spec. Each is one planned exercise, with targeted rechecks after a correction.

**The camera pass** is headed, on the M1, for framing and layout only. It never judges how input feels. It checks against the teacher's criteria:
- **Starting view:** the cell is readable, movement is understandable, and there is useful space around it.
- **Fully zoomed out:** students can explore and find resources while some mystery remains. Resources should usually look as if they were already there, not visibly sprinkled into existence; occasional exceptions are acceptable.
  - The direct check: pan and zoom out just before a pool or ring spawn.
  - If spawning shows, **report it** before changing placement or timing, which would go beyond camera tuning.
- **Fully zoomed in:** organelle positions can be inspected, organelles selected reliably, and the results of commands seen.
- **Scripted close-ups:** clearly show what is being explained, without the subject clipped behind the HUD or the dialogue.
- **Wave framing:** useful awareness of the threat. It needn't equal the starting zoom for numbers' sake.

**The Chromebook control check** is hand-run on a classroom Chromebook, because the M1 can't show how the controls feel there. It covers:
- the wheel or two-finger scroll, a pinch, the zoom bar and the ± ends: each controllable across the whole range;
- accidental zooming;
- near-miss presses that pan instead of pulling.

### Balance Runs

**The autopilot's declared outcome:** a **win**, with the Level Rating calculated correctly from its Wave Ratings. Its time and rating are **reported**, not asserted, until calibrated; an unexpected rating is never by itself a reason to rebalance.

Each run separates what it **asserts** from what it **reports**:

1. **Competent.** Asserts the win and a correct rating calculation. Reports play time and rating.
2. **Minimum slicers** (exploration). Builds exactly 10 slicers and never rebuilds. On fixed seeds, records the outcome (a win and its rating, lysis, or Starvation) and asserts only that no stuck-state flag is raised. Expectations are set from that evidence later.
3. **Pre-wave exhaustion.** Spends down in Objective 5 until the reachable example. Asserts the predicate, Restart under Objective 5 with the reason line, then the Starvation loss.
4. **The recovery fixtures.** Each asserts that *Can't finish building up* stays false:

   | Fixture | State | Also asserts |
   |---|---|---|
   | **N1** | Objective 5; 2 ATP, 0 G, **1 FA** stored; Starvation just started | The fat backup produces ATP, and the autopilot wins |
   | **N2** | Objective 5; 2 ATP; G just spent with **a cycle under way**; a glucose gem within reach once credited | The autopilot wins |
   | **N3** | Objective 5; ATP **exactly the cheapest pull**; a glucose gem inside that pull's reach | The autopilot wins |
   | **N4** (key regression) | Late in wave 2; 2 ATP, 0 G, 0 FA; **enough live slicers** for the remaining viral RNA | An **idle** autopilot wins before Starvation expires |
   | **N5** | Exhausted in Objective 5, with a glucose gem the **membrane's motion collects** | The pickup happens, and the student recovers |
   | **N6** | Objective 5 with the missing ribosomes **already ordered** | Objective 5 completes when they arrive |
   | **N7** | Objective 5; 2 ATP; **G = 1 held** | The student recovers |
   | **N8** | Objective 7; the first slicer purchase already made; the missing slicers **ordered**; no further purchase needed | Objective 7 completes |

5. **A losing fixture.** A fixed seed in wave 2 with stated remaining enemies, existing and pending slicers, membrane HP and resources, chosen so the cell can't win. Asserts lysis or Starvation, and no stuck-state flag.
6. **Partial batches (D18), headless.** With 30 AA, Buy Ribosomes orders 3. Asserts the purchase result (requested 5, bought 3, cost for 3, reason: amino acids) and that Objective 4 completes. A separate UI test checks the honest partial-fill message.
7. **Ribosome limits (D19).** Boundary cases from the real pore and ordering rules: starting from 20 ribosomes, repeated partial orders, and orders pending delivery. Each result is checked against the fork source. If different purchase histories reach different limits, the behaviour and its disposition are documented here.
8. **The dish's edge.** Asserts no pool debit for a roll with no room (D15).
9. **Resource acquisition.** A seeded play-through that actually gathers from both rings and the pools and reaches the ribosome and slicer milestones, checking the ring spacing and D16 timings against the Baseline.
10. **Clamp comparison.** Lens-clamped against unclamped pull cost, reporting time-to-win.

The stuck-state search runs on every Level 2 sweep ([`levels.md`: Testing](../levels.md#testing)).

### Unit tests (core)

- Objective 2: each discovery recorded once and kept after the resource is spent; both holds required; same-tick pickups queue both holds, amino acids first
- the ≥ 5, ≥ 20 and ≥ 10 thresholds at 4/5, 19/20 and 9/10, including a batch that jumps past the threshold
- a first-purchase Objective completes on a partial fill of 1 unit, and not on a purchase that orders nothing
- partial fills: units bought one at a time, only ordered units charged, reported requested and bought quantities and the reason
- caps count ordered units; the pore check per unit (D19)
- slicers: used up on cutting, their AA refunded, no ATP charged to attack
- both rings: *n* distinct positions at *i* ÷ *n* of a turn (D17); left-behind gems credit their pool by +1
- the D16 effective timings for the pools, repair, surplus and dormant ticks, in core ticks at 60 Hz
- membrane repair skips below the repair cost and resumes above it
- the fat backup at G < 1, at FA 0 and 1, and at ATP 249 and 250
- the wave reward credited exactly once, on acknowledgement
- the dormant timer resets on an escape and on a virion birth
- the mitochondrion's actions refused by the core
- no ribosome purchase before Objective 4 is armed, including during Objective 3's completion linger; no slicer purchase before Objective 6 is armed
- the escaped hold: not while any virion or viral RNA of the wave is live, even with viruses dormant; fires when none is live and the dormant count is above 0; once only; in wave 2 if wave 1 never met the condition; opens after the escaped-hold delay
- the predicate: each of its four conditions just true and just false; it never fires after wave 1 is sent; it stays true once true
- condition 3 with a purchase still affordable: at 2.5 ATP, 19 ribosomes and 0 slicers, one ribosome can be bought, yet the predicate is true because the slicers still needed cost more than the ATP held; in Objective 7 with the missing slicers already ordered and no pickup needed, an exhausted cell is false (N8)
- the nucleus Loss Route answers impossible throughout
- the watchdog: the Buy highlight clears once present plus ordered units reach the target; the wave highlight is suppressed at the slicer cap
- the HUD reveals on their Baseline triggers (the zoom control on Objective 4's completion; the membrane gauge when the alert ends)

### Defect Patches

| Defect | Which test must fail |
|---|---|
| The predicate fires during waves | N4 |
| The predicate ignores stored fat | N1 |
| The predicate ignores ordered units | N6 or N8 |
| The predicate ignores a passive pickup | N5 |
| All-or-nothing batches (D18 undone) | Script 6 |
| A partial fill that doesn't count for Objective 4 | Script 6 |
| Objective 2 as an inventory check at the end | The discovery unit test |
| Exact equality for the 20 or 10 target | The threshold unit test with a batch jumping past it |
| The wave reward credited twice, or per kill | The reward unit test |
| Frame counts used as core ticks (the economy twice as fast) | The D16 timing unit test |
| The mitochondrion's Toggle executable in the core | The action-refusal unit test |
| Buy Ribosomes enabled from the How to build hold | The linger purchase unit test |
| The escaped hold on the first escape | The escaped-hold unit test |

## Appendix

### Deviations

| Deviation | Pedagogy Rationale |
|---|---|
| **Objective 2 is one checklist** for amino acids and nucleotides (Baseline: two parallel Objectives). Presentation only; both discoveries and their holds are kept. | One checklist keeps both discoveries visible without imposing an order. |
| **No mitochondrion actions in Level 2.** The fork exposes Move, Toggle, Recycle and Divide here, so hiding them is a deliberate restriction, not preservation; the walkthrough doesn't show whether the commercial game exposed them. | Follows our established teaching sequence and keeps Level 2 focused. An untaught Toggle can silently stop the cell's only ATP source in the first level where Starvation can end it. |
| **Partial fills are reported honestly and count** (D18). The fork bought the partial batch, showed "can't afford" and didn't count the purchase. | The student sees what was actually bought and why the rest wasn't, and an order that happened is never missed by the Objective. The purchasing opportunities are the Baseline's. |
| **The "See if any viruses got in" Objective is dropped;** its moment becomes the Viral defence hold. | The Objective only waited for the alert; the teaching beat opens at the same moment. |
| **Hover-triggered cost cards** become an always-visible batch-cost line under each action ([shared defaults: Action Panel purchases](shared-defaults.md#action-panel-purchases)), keeping their prerequisites (≥ 5 ribosomes; ≥ 5 slicers). | Touch input must work (standing rule), and the cost stays in view at the moment of choosing. |
| **Every camera scale is the Baseline's × 2, as candidates** (Tuning: starting zoom, scripted zooms, wave zoom-out, range and steps). Timings are unchanged. | Level 1's reason: the Baseline's numbers framed a 640 × 480 stage, and on today's screens they leave the cell too small to read. Doubling keeps the Baseline's proportions between zooms; it doesn't prove identical framing on every screen, so the values stay candidates until the camera pass (Verification). |

The Deviations shared by every level also apply, including **Nucleotides (NT)** in place of the Baseline's "NA" (`docs/levels.md`).

### Fork defects this level touches

| Defect | Decision |
|---|---|
| **D1:** NT and AA caps never apply | **Kept uncapped** (Baseline as played). Hoarding alone isn't a reason to revisit it. |
| **D4:** the ribosome batch uses the slicer's multiplier | **Fixed.** Both are 5 (checked), so nothing visible changes. |
| **D15:** the pool loses a gem when there's no room | Fixed core-wide (Level 1). Checked in script 8. |
| **D16 family:** off-by-one counters | **Keep the effective timings** as Tuning: pools 16 frames, rolls 72 px, repair and surplus 61 frames, dormant ticks 31 frames. Checked in script 9. |
| **D17:** stacked ring gems | **Fixed in both rings.** Checked in script 9. |
| **D18 (new):** a batch buys units until one fails, then reports failure and isn't counted | **Keep partial fills; fix the feedback and the counting** (Deviations). |
| **D19 (new):** nucleolus pores are never returned | **Kept (Baseline).** In Level 2 it can only lower the effective ribosome limit after partial fills (for example, 23 ribosomes with 1 ready pore), which doesn't affect the target of 20. The refusal is named honestly. Recycling and rebuying first happen in Level 3, whose spec decides it. |

Untouched: D2, D3, D5–D14.

### Sources

- Fork slot 1's level file and source, read for this spec. The corrections found (no per-kill rewards, single-use slicers, fat processing on by default, the mitochondrion's cycle and Level 2 menu, the first wave ring at 1.2 × lens, the dormant timer's reset, the trigger semantics) are recorded in `docs/research/baseline-mechanics.md` §H.
- Walkthrough `docs/baseline/walkthrough/level-2.md`. Its "5* NA" on ribosomes doesn't match the fork, where a ribosome's nucleotide is permanent and unmarked; the fork rules on numbers.
- Biology wording and demonstrations follow `docs/learning-targets.md`: nucleotides are the building blocks and nucleic acids what they make; RNA carries instructions and amino acids supply the material; ribosomes contain RNA and protein; the temporary nucleotide cost is game accounting (one mRNA can make many proteins); slicers keep their qualified analogy.
