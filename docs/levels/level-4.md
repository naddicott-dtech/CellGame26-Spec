# Level 4 — Golgi and lysosomes (working title)

Players see the title **Cleanup Crew** (`docs/story.md`). This file is the full spec, decided with the teacher in [Specify Level 4 — Golgi and lysosomes](../tickets.md#ticket-65).

It follows [`spec-template.md`](spec-template.md): the build contract comes first and the rationale is in the appendix. The rules for every level are in [`docs/levels.md`](../levels.md), the shared numbers and UI rules in [`shared-defaults.md`](shared-defaults.md), and terms follow `CONTEXT.md`. [`level-3.md`](level-3.md) is the most recent worked example.

**Units.** Every frame count and every per-frame quantity here is in **Baseline frames at 30 Hz**, the fork's rate, converted by the rule in [`level-3.md`](level-3.md) (*Units*): durations double in core ticks, speeds halve per tick, and per-frame charges and intervals convert the same way. Never mix the two units.

## Summary

| | |
|---|---|
| Tier | Required |
| Teaches | **LT2:** the Golgi finishes vesicles and sends them where they're needed; lysosomes digest and recycle large things, including damaged organelles and invading viruses; mitochondria divide. **Experienced:** a damaged organelle is recycled and replaced; recycling returns building blocks but not the ATP spent. **Foreshadowed:** the lysosome's route, ER → Golgi → lysosome. |
| Objectives | 9 (beats table) |
| Waves | Wave 1: 5 invaders. The final attack: 10 invaders and 15 injectors, sent together as two waves. |
| Won | Both waves of the final attack defeated |
| Lost | Membrane at 0 (lysis), nucleus at 0, or Starvation |
| Dead End | Possible: the declared predicate *No mitochondrion left to divide* classifies as a Dead End when ATP is at or above the Starvation Line and no peroxide is left (see *Win and loss*). Two more candidates await validation in the build. |
| Level Rating | The shared rules, from 3 waves weighted 5, 10 and 15 |
| In-level time | About **8 min** for a typical student (working range about 7–10 min): about 3½ min of play and about 4½ min of teaching. A **scheduling estimate**, not a deadline (`docs/levels.md`, *The class frame*); checked by playtest. |

Level 4 pays off Level 3. There, a damaged mitochondrion was a permanent loss of income. Here the student learns the remedy: recycle the damaged one with lysosomes, and divide a working one.

## Shared infrastructure used

This section links to the shared systems; don't rebuild any of them for this level. The list comes from [`infrastructure-index.md`](infrastructure-index.md).

| Shared system | Where | Level 4 specifics |
|---|---|---|
| Game Shell state machine | `architecture.md`: Game Shell | Pause and the encyclopedia exist from the first frame. |
| Objectives, and the minimum beat | `architecture.md`: Simulation Core; [shared defaults](shared-defaults.md#objectives-completion-and-the-linger) | 9 Objectives. The counted ones are **state predicates** (Beats). |
| Story Hold | Same; [shared defaults: Story Holds](shared-defaults.md#story-holds) | 11 holds, 1 of them mid-Objective (beats table). |
| Game Events | `architecture.md`: Game Events | Engulfing, selection, purchases, recycling (started, lysosome fused, cancelled, finished, refunded), division, lysosome meals, peroxide hits, and wave events feed Objectives and the watchdog. |
| Loss Routes, Starvation | [`levels.md`: shared end-of-level rules](../levels.md#the-shared-end-of-level-rules) | **Starvation is on**, with the shared values. All three Loss Routes can be live (*Win and loss*). One advice line is added (below). |
| Win-impossible predicates, Dead End | Same | One declared predicate and two candidates (*Win and loss*). |
| The Restart button | Same; [shared defaults: Restart](shared-defaults.md#restart) | The shared rule. Familiarity never grants an action ([shared defaults: Restart](shared-defaults.md#restart)). |
| Idle watchdog | [shared defaults](shared-defaults.md#the-idle-watchdog) | Progress and highlight per Objective (beats table). |
| Spark preview and refusal | [shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) | Used unchanged. |
| Tuning | `architecture.md`: Content; [shared Tuning](shared-defaults.md#tuning) | This spec's Tuning section. No shared value is overridden. |
| Level Rating | [`levels.md`](../levels.md#the-level-rating) | Waves of 5, 10 and 15. |
| Session Log | [Define the Session Log and save format](../tickets.md#ticket-15) | Also records which mitochondrion was recycled (working or damaged), recycles started, cancelled and finished, divisions, and lysosomes lost to peroxide. |
| The adapters | `architecture.md`: Renderer | No new adapter. |
| Selection and touch targets | [shared defaults](shared-defaults.md#touch-and-selection) | Mitochondria, peroxisomes, lysosomes, the Golgi, the ER, the nucleus and slicers are selectable. The Golgi is claimed by tapping it. The cell is crowded here: the playtest touch check covers it. |
| The world edge | [shared defaults](shared-defaults.md#the-world-edge) | Level 4 declares its own geometry and look (Starting state). |
| Left-behind gems | [shared defaults](shared-defaults.md#gems-not-drawn-from-a-pool) | No scripted gems in this level. |
| Balance Runs and the stuck-state search | [`levels.md`: Testing](../levels.md#testing) | Verification. |

**Starting state is declared, never carried over.** Level 4's content declares its full starting state, including the actions each organelle offers, and the core enforces availability (`architecture.md`: Content). First entry, Restart and replay from level select all start the same.

**Baseline simulation systems used here for the first time.** The catalogue (`docs/research/baseline-mechanics.md`, corrected in §J) fixes their rules, so they need no design ticket. They are shared systems, listed in the infrastructure index and owned by the build, **never implemented privately inside Level 4**:
- **the Golgi** and the lysosome's production route through it (§3.1, §3.2, §J)
- **lysosomes** (§3.1, §5.2, §J)
- **organelle recycling by lysosomes:** the digestion vesicle, borrowed lysosomes, the acid, the refund and Cancel Recycle (§1.4, §J)
- **mitochondrion division** (§1.4, §3.1, §J)
- **invader viruses** (§5.2, §J)

Level 3's systems are used unchanged: small-unit recycling, membrane growth and shrinking, peroxide and peroxisomes, DNA repair enzymes and producer damage.

**Extensions needed.** They block the Level 4 **build slices that use them**, not this spec (the map's *Sequencing* note). If one changes an Objective, a timing or an available player action here, that part of this spec is revisited.
- [Concurrent waves: presenting and acknowledging waves sent together](../tickets.md#ticket-93) extends [the wave presentation contract](../tickets.md#ticket-84). The final attack sends two independent waves at once.
- Used unchanged: [the camera](../tickets.md#ticket-82), [Action Panel purchases](../tickets.md#ticket-83), [wave presentation](../tickets.md#ticket-84), [group selection and group actions](../tickets.md#ticket-87) (its rule that ATP charged now is shown apart from materials returned later also covers organelle recycling), and [organelle Move and Toggle](../tickets.md#ticket-88) (peroxisome Move uses it).

**Open dependency:** [Verify the mitochondrion's production cycle and check the Starvation warning against it](../tickets.md#ticket-85). The metabolism row, and the peroxide rate that follows from it, aren't build-complete until it's resolved.

## Starting state

| | |
|---|---|
| Resources | **Starting resources** (Tuning) |
| Units | **Starting units** (Tuning): 1 working mitochondrion, 25 ribosomes (the cap), 5 peroxisomes, no lysosomes, no slicers, no pending production. The nucleus and the ER are present. The **Golgi** arrives at Objective 2. The **damaged mitochondrion** arrives when the Start hold closes. |
| Actions | Declared at load (Baseline menus): **Nucleus:** Buy Ribosomes (refused "at maximum" all level), Buy Slicers, Buy DNA Repair. **ER:** Make Membrane, Take Membrane, Buy Peroxisome (no auto-replace). **Mitochondrion:** Move, Toggle. **Peroxisome:** **Move** (new here, using the Move and Toggle contract; no hold teaches it separately; the scripts may add a line). **Lysosome** (once one exists): Move, Recycle. The **Golgi** has none until its arrival. The rest are enabled when their Objective is armed (Beats). |
| World | The same lab petri dish as Levels 1–3, **declared again in Level 4's own content**: the **dish radius**, world-fixed bands and a drawn rim, with the cell at the centre. Pulls are clamped by the **edge clamp**. |
| Membrane | 15 nodes and 100 HP max (**membrane**, Tuning), so the lens is about 1,191 px |
| Camera | The **starting zoom**, with the zoom control shown |
| HUD | The ATP, NT, AA, FA and glucose meters, the membrane-health gauge, the Objective pane, Pause and the encyclopedia button, from the first frame. No reveals. |
| Pools | The glucose and AA pools (**pools**, Tuning) are **on from the first frame**. There is no NT or FA pool in this level. |
| Producers | The working mitochondrion burns glucose from the start (**metabolism**, Tuning). Cytoplasm glycolysis doesn't run while any mitochondrion exists. **Fat processing is on.** **Peroxide production is on from the first frame** (no burst), with none on the first cycle. |
| Movement | Free from the first frame |

## Beats

Every row with a hold is a **Story Hold**. The scripts ticket ([Script the ground trials](../tickets.md#ticket-73)) writes the lines; this table fixes what each beat covers. "Mid" marks a hold that fires inside an armed Objective.

| Story Hold | Objective | Completes when (a state predicate) | Switches on | Movement | HUD reveals | Watchdog progress and highlight |
|---|---|---|---|---|---|---|
| **Start.** The lab is getting hot. The cell needs a second mitochondrion for the journey, and one is floating in. | 1. **Get another mitochondrion** | Mitochondria **≥ 2** (a mitochondrion counts from when it is created at engulfment, Baseline) | **When this hold closes:** the **damaged mitochondrion** is placed (Scripted spawns) | Free | — | A pull toward it, or its distance to the membrane shrinking; the damaged mitochondrion (off-screen arrow while outside the view) |
| **Damage.** It's damaged: heat has broken it, and it makes nothing. This cell can't fix a damaged mitochondrion, so it recycles it and makes a new one. Small units are recycled in the cytoplasm; big things like organelles need lysosomes, and lysosomes come from the Golgi. The Golgi appears as a new thing and pulses. | 2. **Get the Golgi** | The student taps the Golgi, within the shared touch target, **and it has arrived** | The Golgi arrives (the Baseline's arrival, as the ER in Level 3) | Free | — | None; the Golgi |
| **Golgi.** The payoff of Level 3's line: this is the Golgi the cell was promised. It finishes vesicles from the ER and sends them where they're needed. How to buy lysosomes. | 3. **Make some lysosomes** | A Buy Lysosome order is accepted for **at least 1 unit** (a partial fill counts, D18) | Buy Lysosome, **when Objective 3 is armed** | Free | — | None; the Buy Lysosome action (the Golgi highlighted first) |
| **Pipeline.** On rails, with the **pipeline zoom** to the nucleus: the RNA instructions reach a ribosome, which docks at the ER; the protein goes into the ER, which buds a small vesicle; the vesicle travels to the Golgi's receiving face; the Golgi finishes it and a lysosome leaves from the shipping face. **The demonstration follows a lysosome**, and never implies that membrane vesicles in play now take this route. One short line connects it to Level 3's simplified route (membrane vesicles in the game still go straight from the ER). **The RNA never turns into the product.** Foreshadowed tier. | 4. **Watch the process** | Lysosomes **present ≥ 1** | — | Free | — | Each pipeline stage reached; none (the student is waiting) |
| **Lysosome.** A lysosome is an acid-filled vesicle. It digests and recycles large things. It takes several to digest a mitochondrion. | 5. **Make 10 lysosomes** | Lysosomes **present ≥ 10**. A student already at 10 completes after the introduction and the minimum beat (D11 fixed). | — | Free | — | Each order or lysosome appearing; Buy Lysosome, **cleared** once lysosomes present plus ordered reach 10 |
| **Recycle.** How to recycle a mitochondrion. An **illustrative** on-rails demonstration (not the student's own organelle): a digestion vesicle wraps the organelle, lysosomes gather and fuse with it, the acid breaks it down, its building blocks come back, and the lysosomes leave again. It costs ATP now; the building blocks return when it's done. | 6. **Recycle the damaged mitochondrion** | **At most 1 mitochondrion is left, and every mitochondrion recycling started has finished with its refund credited, or been cancelled.** "Its refund" is the completed recycling operation's own refund, whichever mitochondrion it was (the working one included, Baseline). | Recycle on **mitochondria and peroxisomes**, **when Objective 6 is armed** (Baseline); Cancel Recycle on an organelle being digested | Free | — | A recycle started, each lysosome fused, the refund; the **damaged mitochondrion**, then its Recycle action |
| **Divide.** That's a lot of building blocks back. Mitochondria can divide to make another working one. | 7. **Divide the healthy mitochondrion** | Mitochondria **≥ 2** (a daughter counts when it appears, Baseline) | Divide on mitochondria, **when Objective 7 is armed** | Free | — | A Divide accepted; the working mitochondrion's Divide action (any mitochondrion's if none works) |
| **Invaders.** R.Y.E.'s "final exam": a new strain that gets inside whole. | 8. **Survive the invaders** (armed when this hold closes, **at the send**) | Wave 1 defeated **and** the How invaders work hold has closed **and** wave 1's defeated card has been acknowledged | **Wave 1** is sent when this hold closes | Free | The wave display (wave ticket) | Any drop in the wave's live plus dormant count; Buy Lysosome, **suppressed** when the cap blocks another purchase |
| *Mid:* **How invaders work.** Opens when wave 1's **alert ends** (**alert duration**). Invaders push through the membrane, damaging it on the way in and on the way out; inside, lysosomes eat them; slicers still cut their RNA. | (Objective 8) | — | — | — | — | — |
| **Final attack.** After wave 1's defeated card is acknowledged. R.Y.E. taunts: everything he has left, at once. | 9. **Survive the final attack** | **Both** waves of the final attack defeated **and** both of their defeated cards acknowledged | **The final attack** (two waves) is sent when this hold closes | Free | — | Any drop in either wave's live plus dormant count; as Objective 8 |
| **Win.** After the last card. R.Y.E. approves, barely (a candidate line: "Provisionally. It remains untoasted."). The Tardi-President moves the launch up. Then the results screen, then the Launch cutscene (`docs/story.md`). | — | — | — | Locked | — | — |

### When effects happen

As Level 3 ([`level-3.md`](level-3.md), *When effects happen*): completion effects on completion; the next hold opens when the completion linger ends; its *Switches on* effects apply when it closes, and then the next Objective is armed, which is when its action is enabled. The Baseline's per-Objective delays, including the 135 frames after the second mitochondrion is engulfed, are replaced by the shared completion linger: none of them times a demonstration.

### Notes on the beats

- **Actions are enabled when their Objective is armed** (Buy Lysosome with 3, Recycle on mitochondria and peroxisomes with 6, Divide with 7), and the core refuses each before then, including during the previous Objective's completion linger. Each stays available for the rest of the level. Everything Level 3 taught is available from the first frame (Starting state). **Familiarity never grants an action:** fast-forwarding a hold on a Restart never enables Divide before Objective 7 is armed.
- **The wrong mitochondrion (Baseline kept).** Recycle is offered on **every** mitochondrion once Objective 6 is armed, and Objective 6 counts mitochondria, so recycling the working one completes it too. The cell then has no ATP income: the damaged one makes nothing, and glycolysis runs only with no mitochondria. The way back is Divide, which has no health check (Baseline): dividing the damaged one gives a full-health daughter, if the student can pay for it. **Clarity comes from presentation, not a restriction:**
  - the damaged mitochondrion must look **unmistakably damaged** at normal gameplay zoom (an art requirement);
  - selecting a mitochondrion shows its condition in the Action Panel;
  - Objective 6's highlight points at the damaged mitochondrion.

  Playtests record which mitochondrion students recycle, **with representative art**. Placeholder art is never evidence for a gameplay restriction. The teacher's classroom experience is that the original's art made this step clear.
- **Present and ready are different.** A lysosome is **present** from when it starts growing at the Golgi (Baseline), which is what Objectives 4 and 5 count. It is **ready** when its grow animation ends. Only a ready lysosome can be called to a digestion vesicle or an invader. A fused lysosome is neither until it buds back out.
- **Objective 6 waits for the refund,** so it can't complete between a mitochondrion dying and its building blocks arriving (as Level 3's Objective 2). A cancelled recycling doesn't block it.
- **Objective 8 is armed at the send** (a stated sequencing adaptation). The Baseline arms it after the invader tutorial. Here:
  - the simulation runs exactly as agreed until the How invaders work hold opens; arming changes completion tracking, not combat time (an unarmed Objective never stops play);
  - the How invaders work hold **always** opens, even if wave 1 is defeated unusually fast;
  - Objective 8 completes only after that hold has closed and wave 1's defeated card (and so its reward) has been acknowledged.

  This also removes the Baseline's latent stall, where a one-shot "wave defeated" event could land before its Objective was armed.
- **The final attack is two independent waves** (Baseline): separate alerts, displays, Wave Ratings, cards and rewards, presented by the concurrent-waves extension. Each card credits its reward exactly once, in the core. Objective 9 needs both waves defeated and both cards acknowledged, in either order.
- **Lysosomes are borrowed by recycling, never used up** (Baseline). The recycle returns the same number of lysosomes, so Objective 6 doesn't cost the student their defence against the invaders. Peroxide can destroy lysosomes at any time (Tuning); the student rebuys them.
- **Starvation advice.** One line is added to the shared table (`docs/levels.md`), keyed to the **Recycle** hold being acknowledged: "A working mitochondrion is being recycled. Cancel Recycle stops it." (working text). It shows only while Cancel Recycle is available **and** cancelling would restore a usable producer: a mitochondrion inside a digestion vesicle whose health is still at or above the stop threshold (acid damage is kept on cancel). Recycle and Divide are never Starvation advice: each costs more ATP than a starving cell has. The loss hints handle them ([hint rules](../tickets.md#ticket-80)).
- **Science accuracy for the scripts.** "Only the nucleus can be repaired" is the game's abstraction, not biology. The Damage hold says this *cell* can't fix a damaged mitochondrion, so it recycles it and makes a new one. Real cells also clear damaged mitochondria through lysosomes, so "recycle and replace" is accurate. The encyclopedia's small print may name it (mitophagy). Mitochondria really do divide (fission).
- **The first-time Starvation card** may open at any point where the warning first shows (shared rule).

## Scripted spawns and rules

### Scripted spawns

| Spawn | Placement | Visibility | Left behind |
|---|---|---|---|
| **The damaged mitochondrion** (Baseline) | When the Start hold closes: one, inside a vesicle wrapper, at a seeded random angle at the **float-in distance** from the centrosome, homing on the centrosome at the **float-in speeds** (Level 1's mitochondrion float-in, catalogue §8.5) | Visible, and unmistakably damaged | Never left behind: it homes until the membrane touches it, and is then engulfed. It isn't a gem and draws on no pool. |

### Rules

| Rule | Trigger (armed between) | Effect | Cooldown and limits |
|---|---|---|---|
| **Pools** (Baseline) | From the first frame to the end: a spawn roll per pool every **roll distance** of travel | One gem just outside the lens, at a random angle (shared pool rules) | Regrowth every **regrowth interval**, with no ceiling (§J). D15: a roll with no room makes no gem and costs nothing. |
| **Purchases** (Baseline, D18) | Each action from its Objective's arming, or from the first frame (Starting state) | The shared purchase rules: unit by unit with honest partial fills; the requested and bought quantities, cost and limiting reason reported (Action Panel contract) | Caps count units **ordered** as well as present. Ribosomes are refused "at maximum". |
| **Production** (Baseline, §3.2, §J) | Any order | As Level 3, plus **lysosomes**: RNA → ribosome at the ER → a small vesicle → the Golgi's **Golgi step** → a lysosome grows at the Golgi (**lysosome grow**) and moves out near it. Membrane vesicles still go ER → membrane, and peroxisomes bud straight from the ER (§I). | NT in flight is the only throughput limit the level adds. NT is a closed budget: the starting 10, the recycle refund and the wave rewards. |
| **Organelle recycling by lysosomes** (Baseline; §1.4, §J) | Recycle on a mitochondrion, from Objective 6's arming | **The core checks the mitochondrion recycle charge first**; if ATP is short, the shared honest refusal and nothing happens. If paid: the mitochondrion stops producing at once; a **digestion vesicle** grows around it and calls the nearest **ready** lysosomes, up to the **lysosomes needed**. Each flies to it and fuses. **Nothing is digested until all have fused.** While fewer are ready, the vesicle waits and calls again every **re-call interval**. Once all have fused, the acid does the **acid damage** every **acid interval**; when the organelle dies it leaves the mitochondrion count at once, and its **mitochondrion recycle refund** is credited **when the organelle recycle animation that follows its death ends** (the one authoritative timing; Objective 6 waits for it), and the same number of lysosomes bud back out, ready again after the **bud time**. | One vesicle per organelle. **Shown as progress, never as a refusal:** the vesicle displays "Lysosomes: *n* of *N*", with "Waiting for *k* more" while it waits; the recycle command succeeded. The Action Panel shows the ATP charged now apart from the building blocks returned later, and that cancelling doesn't return the ATP (group selection contract). |
| **Cancel Recycle** (Baseline) | On an organelle inside a digestion vesicle, from the recycle command until the organelle dies, **including while the vesicle is waiting** | The organelle is released **with the health it has**: acid damage already taken is **kept** (nothing heals a mitochondrion). It produces again only if its health is still at or above the **stop threshold**. Every fused lysosome buds back out; the vesicle is removed. **The recycle charge is not returned.** | Free. Before any acid (while waiting, or while lysosomes are still fusing) it restores a working mitochondrion undamaged. A working mitochondrion drops below the stop threshold on the **5th acid tick** (100 → about 41), so from then on cancelling saves the organelle, which can still be a Divide parent, but not its production. It must stay practically usable: the predicate below relies on it. |
| **Small-unit recycling** (Baseline, §I) | Recycle on peroxisomes (from Objective 6's arming), lysosomes (from creation), slicers, DNA repair enzymes | As Level 3: the charge at the command, the building blocks credited when the unit's recycle animation ends. No lysosome needed. | Refunds 100% (D2 kept). |
| **Divide** (Baseline; §1.4, §J) | From Objective 7's arming, on **any** mitochondrion, damaged or not | The **divide cost**, checked and paid at the command. After the **divide time**, a **full-health** daughter appears beside the parent and counts at once. The parent keeps its own health. The daughter makes no peroxide on its first cycle. | **No health check** (Baseline). The **mitochondrion cap** counts all mitochondria, damaged ones included. Refused while that mitochondrion is already dividing. |
| **Lysosomes and invaders** (Baseline; §5.2, §J) | A virus inside the cell calls the nearest **ready** lysosome every **call interval** | The first lysosome to arrive eats it: the **lysosome meal**. The lysosome survives and is ready again after the meal. | One lysosome per virus. A virus clinging to the membrane can't be eaten. **Newborn virions** of both types, heading out, are eaten too. Killing a virus pays nothing. |
| **Invaders** (Baseline; §5.2) | A wave's invader touches the membrane | After the **invader entry** it does 1 membrane damage (none if vesicle-wrapped; none are here) and moves inside at the inside speed toward the nearest ribosome; there it releases the **invader payload** of viral RNA, each strand making virions at a ribosome, and fades. Virions exit doing 1 membrane damage each. | Escapees re-attack after the **dormant re-attack** time |
| **Slicers** (Baseline) | A live slicer and viral RNA | As Level 3: it cuts the RNA and is used up, refunding its AA | No ATP cost to attack |
| **Waves** (Baseline, §5) | Wave 1 when the Invaders hold closes; the final attack's two waves together when the Final attack hold closes | The **waves** (Tuning), presented by the wave contract and the concurrent-waves extension. On a send with no wave active, the camera zooms out (**wave zoom-out**). | Each wave's defeat, rating, card and reward are its own |
| **Membrane repair** (Baseline, §2.3) | Membrane below max health | As Level 3 | Skips silently while ATP is below the repair cost |
| **Surplus glucose to fat** (Baseline) | As Level 3 | As Level 3 | On all level |
| **Mitochondrion** (Baseline) | Switched on, not damaged, with fuel | As Level 3, including the fat backup. Damaged, switched off, inside a digestion vesicle, or without fuel, it burns nothing, makes nothing and gives off no peroxide. | Its cycle length is [the timing ticket](../tickets.md#ticket-85). Glycolysis runs **only when no mitochondrion exists**. |
| **Mitochondrion and peroxisome Move; Toggle** (Baseline) | From the first frame | The **move costs**, charged continuously; the move stops if ATP runs out (Move and Toggle contract). Toggle is free. | — |
| **Peroxide** (Baseline, §6, §I, §J) | From the first frame, while a working mitochondrion is switched on | As Level 3: the **peroxide rate**, the **targeting weights** (with lysosomes now in the cell, and **D21's band kept**), the **peroxide damage** and speed. A peroxide that reaches a lysosome destroys it (**lysosome health** below one hit); it credits its **lysosome refund** (D23). | Never expires. Never targets the membrane, the ER, the Golgi or ribosomes. |
| **Producer damage** (Baseline, §3.4) | A mitochondrion's health below the **stop threshold** | It stops producing for good. The remedies here are Recycle and Divide. | The Baseline's "no working mitochondrion" dialog is replaced by the shared Restart rule (S1) |
| **Peroxisomes; DNA repair enzymes** (Baseline; reuse Deviation) | From the first frame | As Level 3 | As Level 3 |
| **Spark preview and refusal** (shared) | Any pull | [Shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) | — |
| **Idle watchdog** (shared) | Per the beats table | A non-blocking nudge and its highlight | Shared gaps |

**No tutorial help.** There is no need rule, no floating rescue and no struggle offer. No resource arrives beyond the pools, the refunds and the wave rewards.

## Tuning

Every number is Tuning. Frame counts and per-frame quantities are Baseline frames at 30 Hz. The source is the fork's slot 3 and its source code unless marked. The rows marked *provisional* hold until the build or playtests confirm them.

| Name | Value | Source | Checked by |
|---|---|---|---|
| Starting resources | ATP 500, NT 10, AA 500, FA 50, G 50 | Baseline (the walkthrough agrees) | Unit test |
| Starting units | 1 working mitochondrion (100 of 100 HP); 25 ribosomes (the cap), 5 peroxisomes, no lysosomes, no slicers; no pending production | Baseline | Unit test |
| Membrane | 15 nodes; 100 HP max; max 25 nodes; +10 max HP per node | Code default (the level file's 3 nodes are ignored, D10); Baseline §2.2–2.3 | Unit test |
| Dish radius; edge clamp | As Level 3 (5,000 px; centrosome ≤ dish radius − cell radius) | Baseline; provisional | Boundary invariant |
| Starting zoom; wave zoom-out | As Level 3 | Baseline | Camera ticket |
| Pipeline zoom | Scale 0.6 over 15 frames, then a scroll to the nucleus. **No zoom back** (the student's zoom control). | Baseline | Camera ticket |
| Pools | G 100 at 0.3; AA 100 at 0.3; **both on from the first frame**, regrowth with no ceiling. No NT or FA pool. | Baseline (the fork declares NT 50 and FA 250 pools that never activate; omitted) | Resource-acquisition runs |
| Regrowth interval; roll distance | 16 frames; 72 px | Baseline effective timing (D16) | Unit test |
| Gem values | As Level 2 | Baseline | Unit test |
| Float-in distance; float-in speeds | 1.05 × lens; as Level 1's mitochondrion: 2 px per frame at first, 10 px per frame beyond √1.1 × lens, then 1 px per frame, recomputed every 15 frames (§8.5) | Baseline | Unit test |
| Damaged mitochondrion | 10 of 100 HP (below the stop threshold); never produces; a peroxide target at the mitochondrion weight; stays in the cell at 0 HP (ordinary damage never kills a producer) | Baseline | Unit test |
| Golgi | 250 HP; never damaged in this level; the Baseline's arrival | Baseline | Unit test |
| Lysosome cost; order; cap | **Per unit:** 15 ATP, 25 AA, 1 FA, plus 1 NT tied up in RNA. **Up to 2 per order** (so a full order is 30 ATP, 50 AA, 2 FA and 2 NT). **Cap 25, counting ordered units.** | Baseline §1.4 (the walkthrough's "0/4" and "16" are its made/ordered display, not caps; §J) | Unit test |
| Golgi step; lysosome grow | 60 frames at a Golgi docking point; 90 frames growing, **present** from its start, **ready** at its end | Baseline (medium confidence, from the Flash library) | Unit test |
| Lysosome health; lysosome refund | 3 HP (one peroxide hit destroys it); 25 AA and 1 FA when destroyed (D23) | Baseline | Unit test |
| Lysosome recycle | 8 ATP charged; 25 AA and 1 FA returned | Baseline §1.4 | Unit test |
| Lysosome target | 10 (Objective 5) | Baseline | Unit test |
| **Mitochondrion** recycle charge; refund | 100 ATP charged at the command, never returned; 5 NT, 100 AA, 25 FA, 10 G credited when the organelle recycle animation after its death ends (Rules). **These are the mitochondrion's values only**, not general organelle-recycling values. | Baseline §1.4 (the walkthrough agrees exactly) | Unit test |
| Lysosomes needed (a mitochondrion) | **8, provisional.** A Tuning value, never derived from our art. The fork derives it from its drawn size: about 8, possibly 9. | Baseline reading; **verification is a checklist item** in [the validation task](../tickets.md#ticket-94) | Unit test; the validation task |
| Digestion vesicle | Grows at 2 px per frame to 1.25 × the organelle's size; called lysosomes fly at 5 px per frame and take 17 frames to fuse | Baseline (medium confidence) | Distance-over-30-frames unit test |
| Acid damage; acid interval | About 11.8 HP every 31 frames once all have fused: the damaged mitochondrion dies on the first tick, a working one after 9 (about 279 frames) | Baseline | Unit test |
| Organelle recycle animation; bud time | 31 frames; 30 frames | Baseline (medium confidence) | Unit test |
| Re-call interval | 61 frames while waiting | Baseline | Unit test |
| Divide cost; divide time | 250 ATP, 5 NT, 100 AA, 25 FA, 10 G, at the command; 40 frames, then a full-health daughter 75 px away | Baseline §1.4 (the walkthrough agrees exactly) | Unit test |
| Mitochondrion cap | 10, counting all mitochondria, damaged ones included | Baseline | Unit test |
| Move costs | Mitochondrion as Level 3 (50 ATP per 500 px); peroxisome 10 ATP per 500 px; lysosome 2.5 ATP per 500 px; all continuous | Baseline §1.4 | Unit test |
| Invader | 10 HP; 6 px per frame outside, 3 inside; **invader entry** 30 frames, then 1 membrane damage and 30 px inward; **invader payload** 4 viral RNAs, each making 1 virion | Baseline §5.2 | Unit test; distance-over-30-frames test |
| Call interval | 16 frames (the fork's taunt constant, effective) | Baseline | Unit test |
| Lysosome meal | 80 frames; the virus is killed 25 frames in | Baseline (medium confidence) | Unit test |
| Waves | Wave 1: 5 invaders. The final attack: 10 invaders and 15 injectors, **sent together as two waves**. None vesicle-wrapped. Spawn rings, injector offspring, alert duration and dormancy as Level 2. | Baseline §5 | Balance Runs |
| Dormant re-attack | As Level 2 (15 dormant ticks of 31 frames) | Baseline | Unit test |
| Wave rewards | Wave 1: 1 NT and 13 AA. Final attack: invaders 1 NT and 25 AA; injectors 75 AA. No FA. Each credited once, when its card is acknowledged. | Baseline (the formula corrected in §J; rounded) | Unit test |
| Peroxide rate; peroxide damage; peroxide speed | As Level 3; on from the first frame | Baseline | Unit test |
| Targeting weights | Mitochondrion 10, peroxisome 1, slicer 0.5, **lysosome 2**, each × count; the nucleus gets ⅓ of that sum; the total × 1.05. **D21's band is kept:** the lysosome band is (2 × lysosomes − peroxisomes) wide, empty when peroxisomes ≥ 2 × lysosomes, and the share it loses falls into "no target", which sends the peroxide back to its maker. These are shares of **initial target selection**; peroxisomes intercept peroxide in flight. | Baseline §6, §J | Statistical unit test |
| Mitochondrion health; stop threshold | As Level 3 (100 HP; stops below 50) | Baseline §3.4 | Unit test |
| Peroxisomes; DNA repair; slicers; ribosomes; membrane growth, shrink and repair; nucleus | As Level 3 | Baseline; the shared repair jobs | Unit test |
| Metabolism | As Level 3, including the fat backup. **The cycle length is open** ([the timing ticket](../tickets.md#ticket-85)). | Baseline | Unit test, after the ticket |
| Starvation, lingers, nudges, touch | The shared values; **no overrides** | [Shared Tuning](shared-defaults.md#tuning) | — |

## Win and loss

- **Win:** both waves of the final attack defeated and both cards acknowledged, while Objective 9 is armed, under the shared Objective rules.
- **Loss Routes.** Each answers possible, impossible or **unknown**, as the shared rule allows.
  - **Lysis:** possible while any wave has live virions, viral RNA or a dormant pool, or while the chain of Objectives that sends it can still complete. Before wave 1, nothing damages the membrane.
  - **Nucleus at 0:** **possible** while a working mitochondrion is switched on (it can keep making peroxide). **Unknown** while only peroxide already in flight remains, or while a working mitochondrion exists but is switched off, without fuel, or inside a digestion vesicle that can still be cancelled with its health at or above the stop threshold: remaining peroxide alone doesn't establish lethal damage. **Impossible** when no mitochondrion can produce, none can come (no working mitochondrion to cancel back, no Divide under way), and no peroxide exists.
  - **Starvation:** the shared rule. Possible whenever ATP is below the Line, or an automatic drain could take it there. The only automatic drain is membrane repair, which runs only after wave damage. Spending by choice doesn't count.
- **The exhaustion fact** (a diagnostic, never a predicate on its own) is narrowed as in Level 3: **no remaining route can increase ATP.** In Level 4 the routes are:
  - a working mitochondrion, switched on or off (Toggle is free), with fuel: G ≥ 1, FA ≥ 1, a cycle under way, FA still to come, an affordable pull that could reach a gem, or a gem within motion reach;
  - a mitochondrion inside a digestion vesicle whose health is still at or above the stop threshold, which Cancel Recycle returns to production (acid damage is kept, so after about 4 acid ticks this route is gone);
  - **Divide**, once armed and affordable (ATP and materials), or a daughter already on its way;
  - **glycolysis** when no mitochondrion exists, with glucose held or reachable.

  It is logged for the loss hint ([hint rules](../tickets.md#ticket-80)) and for Balance Run reports. Whether the resources already held can still finish a task is each predicate's own check.

### Declared predicate: *No mitochondrion left to divide*

True when **all** of these hold:
1. **A division is still needed:** Objective 7's predicate has not been satisfied. This reads the Objective's **recorded satisfaction state**, not "not yet completed", so a Divide that has already met it, with only the minimum beat left, never makes the predicate true;
2. **no mitochondrion exists:** none in the cell, **none inside a digestion vesicle that hasn't died yet** (Cancel Recycle can still save it: damaged, perhaps, but a possible Divide parent), and **no daughter pending** from an accepted Divide;
3. **no scripted arrival is pending.** Level 4's one arrival, the damaged mitochondrion, is guaranteed to have been engulfed: Objective 1 needs it, and Recycle (the only way to remove a mitochondrion) is enabled only at Objective 6.

- **Why it is irreversible:** Objective 7 needs mitochondria ≥ 2; Divide is Level 4's only source of mitochondria after the scripted arrival, and Divide needs a mitochondrion. The waves are sent only through Objectives 7 → 8, so the win is out of reach.
- **Not true for:** a working mitochondrion being digested but not yet dead; a Divide accepted and its daughter pending; Objective 7 already satisfied and waiting for its minimum beat; zero mitochondria **after** Objective 7 is satisfied (glycolysis runs, and the student may win on the lysosomes and slicers they have).
- **Reason line** (working text): "No mitochondrion is left to divide."
- **Classification** is the shared rule's, **reassessed continuously**: lysis is impossible (no wave can be sent); the nucleus route is unknown while peroxide is in flight and impossible once none is left (no mitochondrion can make more); Starvation is possible only while ATP is below the Line (glycolysis may restore it, and nothing drains ATP automatically before the waves). So:
  - **ATP at or above the Line, no peroxide left:** every route is impossible, so it is a **Dead End** ("This attempt can't continue").
  - **ATP below the Line, or peroxide still in flight:** a Restart offer under Objective 7. If glycolysis brings ATP back to the Line and no peroxide or automatic drain remains, the same attempt becomes a Dead End.
- **Reachable example:** during Objective 7, with the working mitochondrion the only one left (the damaged one recycled at Objective 6), the student recycles it too and lets the digestion finish. The predicate fires on the tick it dies, as a Restart offer; the Dead End follows when the last peroxide is gone, with ATP at 10 or more. The build demonstrates this example.

### Candidates, to validate in the build

Recorded honestly as **candidates**, not declared. [Validate Level 4's win-impossible predicates in the build](../tickets.md#ticket-94) either finds a reachable example that shows the shortfall and rules out recovery (then declares it, with a regression test) or drops it.

| Candidate | The claim | What must be ruled out | Reason line (working) |
|---|---|---|---|
| **W1 Can't afford the rest** | Before Objective 7 is satisfied: no route can increase ATP, and what is still needed can't be paid from what is held. That means lysosomes beyond those present and ordered, up to 10, while Objective 5 needs them; the mitochondrion recycle charge while Objective 6 needs a recycle; the Divide's ATP and materials. All at per-unit costs (D18). | Every route in the exhaustion fact, above all an affordable Divide once armed and Cancel Recycle of a working mitochondrion. Materials that refunds or pools can still supply. | "No working mitochondrion, and not enough ATP left to replace it." |
| **W2 No defence left** | A wave has live or dormant viruses or viral RNA; no lysosome and no slicer is present or ordered; no route can increase ATP; ATP is below the cheapest defender under the real purchase rules (a slicer's 5 ATP; a single lysosome's 15 ATP under partial fills) | Any defender already ordered or growing; any ATP route. Always a Restart offer (the wave keeps damaging the membrane), never a Dead End. | "No lysosomes or slicers left, and no ATP to make more." |

- **Dead End status:** possible through *No mitochondrion left to divide*. The candidates may add more; until the validation task settles them, none is claimed.
- **Results screen:** "Level complete!", the Level Rating from the three Wave Ratings, and the play time ([shared defaults: Clocks](shared-defaults.md#clocks-and-the-results-screen)). No best time. Then the Launch cutscene.

## Verification

### Time targets

| Measure | Clock | Population | Target | Checked by |
|---|---|---|---|---|
| **In-level time** | From the first Story Hold to "Level complete!", in real time | A typical student (±1 SD) | About **8 min** (working range about 7–10): about 3½ min of play and about 4½ min of teaching. A **scheduling estimate**, reported, never a pass/fail criterion. | Human playtests |
| Teaching budget | Story Holds | A typical student | About 4½ min across 11 holds | Human playtests; the scripts ticket writes to it |
| Stuck points | Where students stall, and whether they recover; **which mitochondrion they recycle** | All playtesters | Recorded, not targeted | Human playtests; the Session Log |
| **Bot regression band** | Play time | The competent autopilot | 150–330 s, provisional and **reporting-only** until calibrated | Balance Runs |

- The walkthrough (6:21 in the level, 2:34 of play) is a practised player's.
- **If playtests run long, investigate avoidable delay first:** confusing controls, a hard-to-find target, or repeated explanation. Time spent making meaningful choices or fighting a hard wave doesn't automatically need shortening. The economy and the three waves stay.

### Resource-band table (required)

"Reach" is the cheapest pull whose membrane touches the nearest glucose gem.

| State | What moves the student out, or ends the level |
|---|---|
| Objective 1, the damaged mitochondrion still drifting in | It homes on the centrosome and arrives without help; pulls toward it only speed that up. The working mitochondrion runs the economy. |
| ATP below the Starvation Line, a working mitochondrion switched on, G ≥ 1 or a cycle under way | It credits ATP within one cycle. The warning may show briefly ([the timing ticket](../tickets.md#ticket-85)). |
| ATP below the Line, G = 0, FA ≥ 1, a working mitochondrion | The fat backup |
| ATP below the Line, the working mitochondrion **switched off**, with fuel | Toggle it on (free); the Level 3 advice line |
| ATP below the Line, the working mitochondrion **inside a digestion vesicle**, not yet dead | **Cancel Recycle** (free) restores it **if its health is still at or above the stop threshold** (acid damage is kept); the new advice line. The recycle charge is lost. Past that point, cancelling keeps a damaged mitochondrion, a Divide parent but no income. |
| From the cheapest pull up to the Line, G = 0, FA = 0, a working mitochondrion | Starvation is running. Pulls inside the spark roll for glucose. Out by finding glucose, or **lost to Starvation**. |
| **No working mitochondrion**, before Objective 7 is armed (peroxide damage, Objectives 1–6) | No ATP income until Divide is armed. Banked ATP must cover the lysosomes still needed, the recycle charge and the Divide. Short of that: candidate W1. |
| No working mitochondrion, Objective 7 armed or later, Divide affordable | **Divide any mitochondrion**, even a damaged one: a full-health daughter restores income. |
| No working mitochondrion, Divide not affordable, no ATP route | Candidate W1 before Objective 7 is satisfied. After it: the waves on banked defences (lysosomes aren't used up), or recycling every damaged mitochondrion to start glycolysis (100 ATP each), or Starvation once ATP falls below the Line, or lysis. |
| **No mitochondrion at all**, a division still needed | **No mitochondrion left to divide:** a Restart offer, or a Dead End at ATP ≥ 10 with no peroxide left. |
| No mitochondrion at all, Objective 7 satisfied | Glycolysis, 2 ATP per G. The student may win on existing lysosomes and slicers. |
| Recycle a mitochondrion with ATP below the recycle charge | Refused honestly; nothing happens |
| A digestion vesicle waiting, fewer ready lysosomes than needed | Progress shown ("Waiting for *k* more"); buy lysosomes; lysosomes busy eating return when done; Cancel Recycle is available throughout. Short of ATP for more: the existing lysosomes may be enough once they are ready. |
| Lysosomes destroyed by peroxide | The lysosome refund comes back; rebuy at 15 ATP each. Objective 5 is a state, so it simply needs the count again. |
| Divide with materials short (NT 5, AA 100, FA 25, G 10) | The recycle refund covers nearly all but the ATP (the walkthrough agrees); the pools supply AA and G; surplus glucose makes FA. |
| NT short for an order (a closed budget) | Wait for RNA to break down; the honest temporary-cost refusal (Action Panel contract). |
| AA short for an order | The AA pool, refunds, wave rewards; a partial fill buys what can be paid for (D18). |
| Nucleus damaged, ATP ≥ a DNA repair enzyme | Buy repair (reusable). Nucleus at 0: **lost**. |
| Membrane damaged, ATP ≥ the repair cost | Repair spends ATP and can push it below the Line. Below the repair cost, repair pauses, and further entries and exits risk **lysis**. |
| A wave under way, no more defenders affordable | Existing lysosomes (not used up) and slicers may be enough. If they aren't, the attempt ends in **lysis or Starvation**; candidate W2 when none is left. |
| At the dish's edge | Inward pulls work; a roll outside the dish makes no gem and costs the pool nothing (D15). |

### Balance Runs

**The autopilot's declared outcome:** a **win**, with the Level Rating calculated correctly from its three Wave Ratings. Its time and rating are **reported**, not asserted, until calibrated.

Each run separates what it **asserts** from what it **reports**:

1. **Competent.** Asserts the win and a correct rating calculation. Reports play time, rating, lysosomes lost to peroxide, and lysosomes ready when each wave arrives.
2. **Wrong mitochondrion.** At Objective 6, recycles the **working** mitochondrion with enough ATP left for a Divide. Asserts that Objective 6 completes only after that recycling's refund, that Divide on the damaged one gives a full-health daughter, and the win. A variant without enough ATP reports the outcome (candidate W1's territory) and asserts only that no stuck-state flag is raised.
3. **Cancel Recycle.** Starts recycling the working mitochondrion and cancels, once while the vesicle is waiting and once after all lysosomes have fused but before it dies. Asserts the mitochondrion works again (undamaged when cancelled while waiting; with its acid damage kept when cancelled after 1–4 acid ticks), the lysosomes bud back and are ready, the recycle charge is not returned, and production resumes.
4. **No mitochondrion left to divide.** Reaches the example. Asserts the predicate on the tick the last mitochondrion dies, the Restart offer while peroxide is in flight, and the Dead End at ATP ≥ 10 once none is left. A variant below the Line with glucose held asserts the Restart offer, then the reclassification to a Dead End when glycolysis brings ATP back to 10.
5. **Recovery fixtures for *No mitochondrion left to divide*.** Each asserts the predicate stays false:

   | Fixture | State | Also asserts |
   |---|---|---|
   | **M1** | Objective 7; the last mitochondrion inside a digestion vesicle, not yet dead, past the 5th acid tick (so already damaged) | Cancel Recycle saves it as a damaged mitochondrion; Divide on it gives a working daughter; the autopilot wins |
   | **M2** | Objective 7; a Divide accepted and its daughter pending; the parent then recycled | The daughter appears and satisfies Objective 7 |
   | **M3** | Objective 7 satisfied, its minimum beat running; the last mitochondria then recycled | The predicate stays false; Objective 7 completes; wave 1 is sent |
   | **M4** | Objective 7; both mitochondria damaged by peroxide | Divide on a damaged one restores income; the autopilot wins |

6. **The validation task's candidates** (W1, W2) run under [their ticket](../tickets.md#ticket-94), not here.
7. **The lysosome Objectives.** Asserts: Objective 3 on an accepted order of 1 (a partial fill); Objective 4 when a lysosome starts growing (present), not when it is ready; Objective 5 completing after the introduction and minimum beat when 10 are already present at arming (D11 fixed); a lysosome destroyed by peroxide lowering the count.
8. **Organelle recycling.** Asserts: nothing is digested until the lysosomes needed have fused; only ready lysosomes answer; the vesicle's waiting state is reported as progress, not as a refusal; the refund is credited once; the same number of lysosomes bud back; Objective 6 doesn't complete between the death and the refund.
9. **The invader beats.** On a seed where wave 1 is defeated unusually fast (many lysosomes ready), asserts the How invaders work hold still opens, and Objective 8 completes only after it closes and the card is acknowledged. Asserts no burst of viruses moves while a hold is open.
10. **The final attack.** Asserts both waves are sent on the same tick, each has its own card, rating and reward credited exactly once, the win needs both, and the cards can be acknowledged in either order.
11. **Peroxide targeting.** Headless and statistical: with 1–2 mitochondria, 5 peroxisomes and 0–10 lysosomes, the initial target shares match the weights **with D21's band kept** (empty lysosome band at up to 2 lysosomes; the lost share back to the maker). **Optional evidence** (never a prerequisite to building): the same runs with the band corrected, reporting lysosomes lost and hits on mitochondria, for a later controlled comparison.
12. **Resource acquisition.** A seeded play-through gathering from both pools from the first frame, reporting regrowth with no ceiling and the D16 timings.
13. **The dish's edge.** Asserts no pool debit for a roll with no room (D15).

The stuck-state search runs on every Level 4 sweep ([`levels.md`: Testing](../levels.md#testing)).

### Unit tests (core)

- the starting state declared in content: menus, units and resources the same on first entry, Restart and replay; actions refused before their Objective is armed, including during the previous completion linger (Buy Lysosome, mitochondrion and peroxisome Recycle, Divide); fast-forward never enabling an action
- the damaged mitochondrion: placed when the Start hold closes, at the float-in distance, homing; 10 HP; never producing; staying at 0 HP; Objective 1 at mitochondria 1/2
- the Golgi's arrival and claim by tap
- lysosome purchases: per-unit cost, up to 2 per order, partial fills, the cap of 25 counting ordered units; present from grow start, ready at grow end; only ready lysosomes answer a call
- Objectives 3, 4 and 5 at their boundaries (0/1 ordered, 0/1 present, 9/10 present), including D11's case
- recycling a mitochondrion: the charge checked first and refused honestly; production stopping at the command; no acid before all lysosomes have fused; the re-call interval; the damaged one dying on the first acid tick and a working one on the ninth; the refund once; lysosomes budding back; Cancel Recycle while waiting and after fusion, returning lysosomes but not the charge, and keeping the health the organelle has; production resuming only at or above the stop threshold (4 and 5 acid ticks); the refund credited when the recycle animation after death ends, not at death
- Objective 6: false while a recycle's refund is pending; true after the refund, for either mitochondrion; a cancelled recycle not blocking it
- Divide: cost and time; no health check; a full-health daughter; the parent's health kept; the cap counting damaged mitochondria; refused while already dividing; Objective 7 at 1/2
- the predicate: each condition just true and just false (a mitochondrion in a vesicle, a daughter pending, Objective 7 satisfied but in its minimum beat); stays true once true; classification at ATP 9 and 10, with and without peroxide in flight, and the reclassification after glycolysis
- the nucleus Loss Route's three answers
- glycolysis only with no mitochondrion (a damaged one blocks it)
- lysosomes eating invaders and newborn virions of both types; never a clinging virus; the lysosome surviving the meal
- invaders: entry damage, the payload, one lysosome per virus
- peroxide: on from the first frame, none on the first cycle; destroying a lysosome in one hit and crediting its refund; D21's band kept
- the Cancel Recycle advice: shown only after the Recycle hold, only while a mitochondrion with health at or above the stop threshold is in a vesicle that can still be cancelled (shown at the 4th acid tick, gone at the 5th)
- the wave rewards credited exactly once each, on acknowledgement; Objective 9 needing both cards
- the unit conversion: invader, lysosome and float-in distances over 30 Baseline frames equal the Baseline's

### Defect Patches

| Defect | Which test must fail |
|---|---|
| Objective 5 counted only on a later lysosome event (D11 reproduced) | Script 7 and the Objective 5 unit test |
| Objective 4 counting ready lysosomes instead of present ones | Script 7 |
| A growing lysosome answering a digestion vesicle | The ready-lysosome unit test |
| Digestion starting before every lysosome has fused | The recycling unit test and script 8 |
| Objective 6 met before the refund | The Objective 6 unit test |
| Cancel Recycle returning the charge, or unavailable while waiting | Script 3 |
| Cancel Recycle healing the acid damage | Script 3 and the Cancel Recycle unit test |
| The recycle refund credited at death instead of after the recycle animation | The recycling unit test |
| Divide refused on a damaged mitochondrion | Script 2 and fixture M4 |
| The predicate ignoring a mitochondrion in a digestion vesicle | Fixture M1 |
| The predicate ignoring a pending daughter | Fixture M2 |
| The predicate reading "Objective 7 not completed" instead of its satisfaction state | Fixture M3 |
| No reclassification after glycolysis recovers ATP | Script 4's variant |
| The How invaders work hold skipped when wave 1 falls fast | Script 9 |
| A final-attack reward credited twice, or the win on one wave | Script 10 |
| D21 corrected by accident (the lysosome band at full weight) | Script 11 |
| Glycolysis running while a damaged mitochondrion exists | The glycolysis unit test |
| Divide enabled on a Restart before Objective 7 is armed | The action-refusal unit test |

## Appendix

### Deviations

| Deviation | Pedagogy Rationale |
|---|---|
| **Objective 8 is armed at the send** (Baseline: after the invader tutorial). The invader teaching still always happens, and progression waits for it and for the wave's card. | Reliable completion tracking: a fast defeat can't be missed, and the student sees one clear task while the viruses approach. The combat is unchanged. |
| **State predicates** for Objectives 5, 6 and 9, and Objective 6 waits for the recycle's refund (Baseline: counts checked on events; the join of two final-wave flags). | Work that lands during a completion linger isn't lost, and an Objective can't complete between a death and its refund. |
| **Actions enabled when their Objective is armed** (Baseline: at the preceding tutorial's close). | The same completion-linger fix as Levels 2 and 3. |
| **The Baseline's per-Objective delays are replaced by the completion linger,** including the 135 frames after the second mitochondrion is engulfed. | One shared pacing rule. None of these delays times a demonstration. The build observes the Damage hold's timing. |
| **The digestion vesicle's wait is shown as progress** ("Lysosomes: 5 of 8", "Waiting for 3 more") instead of an alert repeating every 61 frames. The waiting and re-calling logic is unchanged. | The recycle command succeeded; a repeating alert reads as failure. Showing the count makes "several lysosomes digest one organelle" visible, so the lesson doesn't depend on reading. |
| **A Starvation advice line for Cancel Recycle,** keyed to the Recycle hold. | Advice names actions the student can perform, keyed to what they've been taught; cancelling is free. |

The Deviations shared by every level also apply, including **hydrogen peroxide** in place of "free radicals" and **Nucleotides (NT)** in place of "NA" (`docs/levels.md`).

**Not Deviations.** Recycle on every mitochondrion, Objective 6 met by either one, and Divide without a health check are all **Baseline, kept on purpose** (the teacher's classroom evidence: the original's art made the damaged one obvious). Not declaring the unsent waves changes nothing a player sees.

### Fork defects this level touches

| Defect | Decision |
|---|---|
| **D1:** NT, AA and FA caps never apply | Kept uncapped (Baseline as played) |
| **D2:** 100% recycling refunds | Kept |
| **D5:** no cap on DNA repair | Kept (as Level 3) |
| **D10:** the level file's 3 membrane nodes are ignored | The code default of 15 nodes is used |
| **D11:** the lysosome count misspelt in the arming check | **Fixed** by the state predicate (a clear failure, not a Deviation). Checked in script 7. |
| **D15:** the pool loses a gem when there's no room | Fixed core-wide. Checked in script 13. |
| **D16 family:** off-by-one counters | The effective timings kept as Tuning (the pools, the re-call interval, the call interval) |
| **D18:** partial batches | Kept, honestly reported; a partial lysosome order completes Objective 3 |
| **D19:** nucleolus pores never returned | Unreachable: ribosomes start at the cap and can't be recycled |
| **D20:** silent pore refusals | Fixed core-wide (Level 3); not reachable here |
| **D21:** the lysosome targeting band omits the peroxisome weight | **Kept as played.** Fixing it is recorded as a candidate for a controlled comparison (script 11's optional evidence), adopted only if it shows a worthwhile improvement without weakening the challenge. |
| **D23:** worn-out and destroyed units refund | Kept: a lysosome destroyed by peroxide refunds 25 AA and 1 FA; peroxisomes as Level 3 |
| **D24:** the node cap ignores growth in progress | Fixed core-wide (Level 3) |
| **S1:** the "no working mitochondrion" dialog | Replaced by the shared Restart rule (`docs/levels.md`) |
| **The missed-event stall** (a wave defeated before its Objective is armed) | Fixed by arming at the send (Deviations) |

Untouched: D3 (fixed in Level 3; Take Membrane is available here and uses the fix), D4, D6–D9, D12–D14, D17, D22.

### Content the fork declares but never uses

Recorded so nobody "restores" it later, thinking it was lost:
- **Three waves:** 10 injectors, 15 invaders, and 15 invaders half wrapped in vesicles. Nothing in the level file sends them. Not declared in our content.
- **NT and FA pools** (NT 50 at 0.05; FA 250 at 0.3), never switched on. Not declared.
- **The walkthrough's lysosome "cap" of 4, then 16,** is the HUD's made/ordered display (§J). The cap is 25.

### Proofs

- **The damaged mitochondrion always arrives:** it homes on the centrosome at a positive speed until the membrane touches it, and nothing removes it before then.
- **The scripted path is affordable from the start with room to spare on materials:** 10 lysosomes cost 150 ATP, 250 AA, 10 FA (and up to 10 NT in flight); the recycle 100 ATP; the Divide 250 ATP, 5 NT, 100 AA, 25 FA, 10 G, most of which the recycle refund returns. The working mitochondrion earns ATP throughout; the ATP margin depends on how much the student spends on pulls, which is candidate W1's question.
- ***No mitochondrion left to divide* is irreversible:** see its section. The Dead End at ATP ≥ 10 with no peroxide follows from the shared rule.

### Open items carried forward

- **The candidates W1 and W2, the Loss Route unknowns, and the lysosomes-needed value (8, provisional):** [the validation task](../tickets.md#ticket-94).
- **D21's controlled comparison:** optional evidence in the same task, never a build prerequisite.
- **Concurrent waves:** [the extension ticket](../tickets.md#ticket-93).
- **The metabolism cycle:** [the timing ticket](../tickets.md#ticket-85).
- **The damaged mitochondrion's art** must read as damaged at normal zoom: an art requirement for the art pipeline, checked in playtests with representative art.

### Sources

- Fork slot 3's level file and source, read for this spec (clean room; nothing copied). The corrections found are recorded in `docs/research/baseline-mechanics.md` §J.
- Walkthrough `docs/baseline/walkthrough/level-4.md`. Its lysosome "cap" and its Golgi "engulf" were misreadings (§J); the fork rules on numbers.
- Biology wording follows `docs/learning-targets.md`: the Golgi finishes vesicles and sends them where they're needed; lysosomes digest and recycle large things, including damaged organelles and invading viruses; mitochondria divide.
