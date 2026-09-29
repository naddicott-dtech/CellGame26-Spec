# Shared level defaults

The numbers and UI rules every level shares. A level spec points here and states only where it differs; it never re-decides these. Decided with the teacher in [Define the shared level defaults and the infrastructure index](../tickets.md#ticket-79), from the evidence in the Level 1 prototype's findings (`docs/prototypes/level-1-findings.md` on the `prototype/level-1` branch; gaps M1–M14 and A1–A7).

- **Where the shared systems live:** [`infrastructure-index.md`](infrastructure-index.md).
- **The shape of a level spec:** [`spec-template.md`](spec-template.md).
- **The shared end-of-level rules** (Starvation, win-impossible predicates, Dead End, the Level Rating): [`docs/levels.md`](../levels.md).
- **Terms** follow `CONTEXT.md`.

**How to read this file.** Every number is **Tuning**, in the [Tuning table](#tuning) at the end. Most are provisional until playtests confirm them. The rules around the numbers are *not* provisional: a builder must be able to change a linger from 1.5 s to 2 s without changing how any system behaves.

## Teacher rules for every level

1. **Tune for the typical student.** That's a student within about ±1 standard deviation, who follows the instructions imperfectly but as best they can. Edge cases (frugal, willfully bad, edge-seeking, idle) only have to stay winnable, or reach the level's defined loss. A level is never rebalanced to make them fast or slow.
2. **No free resources beyond the level's recipe after Level 1.** "The recipe" means the level's Baseline pools and scripted spawns. Level 1's need rule, floating rescue gem and struggle offer are tutorial help and are **not inherited**. From Level 2 on, students "just have to get good". The idle watchdog and the spark preview are not help in this sense; they apply everywhere.
3. **The lesson must not depend on reading.** Every essential idea is also *shown*: an on-rails demonstration, a meter, a tag or a cue. The text supports what's shown.
4. **Speed is not a learning target.** Time helps us judge classroom pacing; students are rewarded for completing the learning challenge, not for doing it fastest. See [Clocks](#clocks-and-the-results-screen).

## Objectives: completion and the linger

The shared Objective rules (one at a time, introduction acknowledged, the minimum beat) are in `architecture.md`. This section fills in the completion transition.

- **An Objective completes once.** The core marks it complete on one tick and applies its completion effects exactly once.
- **The completed Objective stays on screen for the completion linger**, with play running. The **next Objective is not armed** until the linger ends *and* its introduction is acknowledged.
- **Events during the linger never count toward the next Objective.** Only events while an Objective is armed count toward it. A pull made during a linger is not carried over.
- **A win or a loss takes effect at once.** The linger never delays either, so an already-won student can't die during it.
- **The minimum beat and the completion linger are separate Tuning entries.** They share a starting value; they are not the same rule.

## Story Holds

The shared Story Hold rule is in `architecture.md`.

- **A hold freezes gameplay on the tick it triggers.** It does not wait for the cell to stop moving, so a cell caught mid-pull stays in its current (possibly elongated) pose. This is accepted provisionally.
- **Revisit only if playtests show** the pose looks broken or hides a demonstration. The first thing to try then is a **purely visual** settling animation while gameplay stays frozen. It must never move the authoritative membrane, collect gems, spend resources or advance hazards. Whether it convinces needs a small experiment, not a spec change.
- Rejected: a "pending hold" that let the sim run until the cell settled. It would let damage and Starvation continue, and even a loss happen, before the hold opened, and it needed exceptions for urgent holds.

## The pseudopod spark preview and refusal

Every level has pseudopods, so this applies to every level. It keeps the Baseline's all-or-nothing refusal: an unaffordable pull is refused, never shortened.

**Deviation (UI only):** the spark preview and the refusal cues replace the Baseline's alert. **Pedagogy Rationale:** students read ATP as the price of a visible action, and showing how far the current ATP reaches turns a refusal into the lesson "cost grows with distance".

### One core calculation

- **The core resolves every attempted pull**, from the grab point and the pointer, against the lens clamp and the world boundary. It reports the resolved target, the cost, whether it is affordable, and whether it is a valid pull (past the minimum drag, and a real pull). The cost uses the clamped target (the Deviation in `docs/learning-targets.md`).
- **The preview, the affordability colouring, the ATP charge and the actual movement all use that one answer.** The renderer draws it; it never computes its own circle or cost. Otherwise the arrow could promise one move, the cost preview price a second, and the cell make a third. Near the world edge, where the boundary clamp changes the priced destination, a plain affordable circle would classify pointer positions wrongly.
- **Affordability and validity are separate conditions.** A release on an affordable part of the arrow can still fail to launch because it is below the minimum drag, or otherwise not a valid pull.

### The preview while dragging

- **The arrow** runs from the grab point to the pointer (Baseline). Cost and direction come from the centrosome → target line.
- **Colouring by position:** each part of the arrow whose release point would be affordable is the ordinary arrow colour; each part that would be unaffordable is grey at the **grey opacity**.
- **The spark** is a small red star-burst of the **spark size**, a fixed screen size that doesn't scale with zoom. It marks the **far** boundary: the point along the arrow where it stops being affordable.
  - If the arrow crosses back into affordability (the grab point lies outside the affordable area, which happens only at very low ATP), that near crossing is only a colour change. A spark there would make "let go before the red spark" false.
  - If the whole arrow is unaffordable, it is entirely grey, with **no spark**. Never invent a spark position that suggests an affordable release.
  - If a full-lens pull is affordable, the arrow is entirely the ordinary colour, with no spark. The spark only appears when it marks a real affordability boundary.
  - The spark's shape, not just its colour, carries the meaning, so colour-blind students can read it.
- **Below the minimum drag** (measured from the grab point), the arrow is a short dashed stub with no cost number. Past the minimum, it gets the normal affordability preview above. It does not simply turn solid.
  - Releasing on the stub clears it quietly: no red, because nothing was refused for cost.
  - Playtests check whether students read the stub. A "Drag farther" line is added only if they don't.
- **Placement:** the arrow, the spark and the stub are drawn in Pixi; the cost number is the DOM cost preview (the placement rule in `architecture.md`).

### The refusal

A release beyond the spark is refused. It must read as **intentional, never as a bug**.

- **The arrow** snaps back to the spark with a red flash, over the **snap-back time**. With no spark (all grey), the arrow fades out with the red flash instead.
- **The ATP meter** pulses red at the same moment (the **meter pulse**).
- **A short line near the cell**, world-anchored DOM, lingering for the **refusal line linger**, names the cause and the fix (working text; the scripts tickets write the final lines):
  - with a spark: "Not enough ATP to reach there. Let go before the red spark."
  - all grey: "That pull costs more ATP than you have. Try a shorter pull."
- **No sound and no modal.**
- **Repeats register every time, without stacking.** There is one arrow effect, one meter-pulse sequence and one line; a new refusal restarts or replaces them. There's no cooldown and no escalation. Pause freezes them.
- **Tone.** A refusal is information, never mockery, in every level. The "skill issue" quip (`docs/story.md`) never appears on a refusal; it belongs to loss and Restart moments in Levels 2+.
- **The Game Event `pseudopod_refused`** carries the required ATP (after clamping), the ATP available, and the distance used for the cost, with the attempted target kept distinct from the clamped one. It gets the shared tick and sequence stamps. The UI and the Session Log react to it. How long the Session Log keeps raw events is for its format ticket.
- **Playtest evidence:** repeated-refusal counts. The spark is a promising fix for the Level 1 soft-lock, not yet proof that students learn how to recover. **The fallback** if playtests show repeated releases past the spark: auto-shorten with a 10 ATP reserve, as a Deviation with its own rationale.

## Restart

Restart is always in the Pause menu, and appears under the Objective when winning becomes impossible (`docs/levels.md`).

- **A fresh attempt.** Restart starts the level again from its starting state, with a **new seed**. The authored content and Tuning are fixed (the wave schedule, recipes, costs, thresholds), but every seeded outcome may differ: spawn positions, spawn-chance rolls, pool regrowth, gem sizes and any other seeded roll. Each attempt records its own seed, so replay stays reproducible.
- **The opening cutscene isn't replayed.**
- **Every Story Hold opens again**, because holds also acknowledge Objectives and gate triggers. There is no second way to acknowledge an Objective.
- **Familiar teaching is shown faster.** A specific hold this student has already seen, on an earlier attempt or an earlier completion of the level, can be **fast-forwarded**:
  - its on-rails demonstration, scripted camera move and text reveal finish with one click or tap
  - it carries a "you've seen this" marker
  - a hold the student hasn't seen yet can't be fast-forwarded, even on a later attempt
- **The fast-forward contract:**
  - fast-forwarding finishes the demonstration correctly, and every effect it must cause happens exactly once
  - Objective acknowledgement still follows the shared rule
  - **the minimum beat is separate:** finishing an animation never bypasses it
  - **familiarity never grants an action:** fast-forwarding, or having seen a hold before, never changes which actions are allowed. Availability follows the level's arming rules on every attempt ([Specify Level 4 — Golgi and lysosomes](../tickets.md#ticket-65)).
- **Once-per-save cards** (the first-time Starvation card) aren't shown again; they stay in How to Play.
- **Level 1's struggle offer** uses the same Restart. How it clears is in [Lingers](#lingers); Restart stays in the Pause menu either way.

### After a loss: the cause and one hint

The loss screen gives the **immediate cause** plus **one actionable suggestion**, using only mechanics the student has already been introduced to. This extends the Starvation rule "advice is possible actions, keyed to introductions" (`docs/levels.md`) to every loss; it is not a second advice system.

- The suggestion comes from a small set of **authored rules** over facts from the attempt's history: prolonged ATP shortage, resource-blocked production, damaged or disabled producers, the wave reached.
- It separates what ended the attempt from what might improve the next one, and never confidently diagnoses from the final state alone (for example, "wrong purchase order").
- If the same failure pattern repeats, the suggestion gets more explicit.
- No automatic difficulty changes and no retry limits without a separate decision.
- The rule table, and the attempt history the core must keep for it, are [Define the loss-screen hint rules and the attempt history they read](../tickets.md#ticket-80).

## Clocks and the results screen

Three clocks, each with its own purpose:

| Clock | What it counts | Who sees it |
|---|---|---|
| **Play time** | Ticks run outside Story Holds and Pause (encyclopedia and menus included) | The student, on the results screen |
| **In-level time** | From the first Story Hold to "Level complete!", holds included; the opening cutscene not counted. **Excludes every Pause reason** (the Pause menu, the encyclopedia, a hidden tab), each recorded separately. Measured in real time, not by counting ticks, because the core caps catch-up ticks after a long frame. | Playtests: the human time target in every spec |
| **Classroom total** | Real time the game was open and visible in one **session** (page load to page close or reload): menus, cutscenes, every attempt and retry, holds, the Pause menu and the encyclopedia. **Hidden-tab time is excluded** and recorded as its own total, so a student on another tab shows up as that, not as slow play. Summed per session and per level. | The Session Log, for pacing |

- **No running timer during play.** Real countdowns, such as Starvation's, stay clearly labelled warnings with their own purpose.
- **The results screen** shows "Time spent playing: 3 min 12 sec" as secondary information, separate from the Level Rating. Finishing faster never improves the Level Rating.
- **No best time.** Best time is neither stored nor shown, and "new best" is kept only for the Level Rating, which is feedback on defensive performance.
  - **Deviation** (the Baseline saves best time). **Pedagogy Rationale:** speed is not a learning target. Removing best-time comparisons avoids encouraging rushed play and keeps attention on completing the level and improving defensive strategy.
- Pause, encyclopedia and hidden-tab time never count toward play time or in-level time. The Session Log records each separately.

## Time targets in every spec

Each spec's Verification section gives both, each with its clock and population named:

- **The human in-level time**, for the typical student, checked by playtest.
- **The bot regression band**, in play time, calibrated from the bots, with no teaching meaning of its own. It catches regressions; it doesn't judge pacing.

## Pause, How to Play and the encyclopedia

- **Pause exists from the first frame of every level**, Level 1 included. A beat may *introduce* it later (Level 1's HUD hold); teaching a control and making it available are separate.
- **The encyclopedia button** may be revealed at a beat in Level 1 (its HUD hold). From Level 2 on, it is present from the first frame.
- **The encyclopedia pauses play.**
- **Pause reasons stack** (the pause button, an open menu, the encyclopedia, a hidden tab). Play resumes only when no reason is left: closing one never resumes play while another still applies. This is one shared rule in the Game Shell, not separate screens each toggling a `paused` flag.
- **Closing the encyclopedia during a Story Hold returns to that hold.**

## Lingers

- **Completion linger:** see [Objectives](#objectives-completion-and-the-linger).
- **Recovery linger:** after Starvation's countdown vanishes, the amber "ATP recovering" indicator lingers for the recovery linger.
- **Level 1's struggle offer** is a choice, and recovery doesn't make restarting an invalid choice:
  - while the struggle condition holds, the offer shows: the line and a button labelled "Restart level"
  - when the condition clears, the line ("Glucose is on its way…") is removed **at once**, because it is no longer true
  - the button alone stays for the **Restart offer hold** (its own Tuning entry, not the recovery linger), long enough to make and carry out a decision
  - if the condition returns, the pending dismissal is cancelled and the line comes back
  - the button is never removed during a press or while it has keyboard focus
- **A linger is a display, separate from the state behind it.** Recovering from Starvation resets the real countdown at once; the indicator only explains what happened. If ATP falls again, the current warning replaces the recovery display at once. No lingering display ever keeps a statement on screen after it stops being true.

## Touch and selection

Touch input must work (a standing rule). Hit-testing belongs to the core (`docs/architecture.md`).

- **Hit area:** an organelle's drawn edge plus the **touch padding**, and never smaller than the **touch floor** on screen.
- **When hit areas overlap:**
  1. a direct hit on an organelle's visible shape beats another organelle's padding
  2. when only padding overlaps, the nearest visible shape wins
  3. true overlaps and exact ties go by a documented, stable order that matches what is drawn on top
- **Padding never steals a membrane drag.** The input adapter's click-versus-drag rule is unchanged: a drag starting on the membrane is always a pseudopod.
- **Screen scale is data.** CSS pixels depend on the displayed scale, so the input adapter passes the scale (or the converted tolerance) to the core as explicit, recorded command data. The core never inspects Pixi or the browser.
- The exact padding needs touch testing in a crowded cell. The ownership and overlap rules don't.

## The idle watchdog

The idle watchdog is in every level (`docs/architecture.md`). It is not tutorial help.

- **The schedule:** the nudge gaps are *gaps*: the first nudge, then the second, then the repeat, so nudges come at 30, 90, 180, 270 s… with the default Tuning.
- **What resets the schedule:**
  - a new Objective starts a fresh schedule
  - meaningful progress on that Objective resets it
  - unrelated activity doesn't
  - Story Holds and Pause don't count as idle time
- **Each Objective declares**, in level content:
  - its **progress condition** (what counts as meaningful progress)
  - a **semantic highlight target:** an organelle, a particular action, a resource meter, the nearest resource of a kind, or none
- **Level content never names a DOM selector or a Pixi object.** The UI resolves the target into a visible element.
- **If the target is an Action Panel button that's hidden** because its organelle isn't selected, the organelle is highlighted first. The watchdog never points at an unavailable control.
- **Off-screen targets** get an arrow at the edge of the play area not covered by the HUD and Action Panel, pointing toward the target, rather than at the browser edge.
- **If the target disappears,** the hint clears or is recomputed.
- **The nudge** is a short, non-blocking line (Aqua's, in Level 1). It is not a Story Hold, and nothing pauses.

## The world edge

- **All levels share the boundary machinery; each level declares its own geometry and look** (size, background, rim). No level inherits Level 1's dish bands.
- **A world-fixed background.** The background is fixed to the world, not the camera, so movement reads, and the rim is drawn, so the space beyond it reads as "nothing out here" (Level 1 playtest, M13).
- **The pull resolver clamps the target** so the centrosome stays inside the level's edge clamp ([One core calculation](#one-core-calculation)).
- **The guarantee:** the cell stays inside the boundary during overlapping movements and growth too, not only at the pull's target. The core has a tested boundary invariant for it.

## Gems not drawn from a pool

- **Every scripted or rescue spawn declares what happens when it is left behind:** either *vanish*, or *credit a named pool by a stated amount* (Level 1's ring returns +1 to the glucose pool, Baseline). There is no default.
- **The type-check enforces the shape,** and content validation confirms the named pool exists.
- **Collection and being left behind are mutually exclusive,** and any credit is applied exactly once, so duplicate cleanup can't create resources.

## Tuning

Provisional unless marked. Each level's own Tuning table may override a value only by naming it here and giving a reason.

| Name | Value | Source |
|---|---|---|
| Minimum beat | 1.5 s | `architecture.md`; provisional |
| Completion linger | 1.5 s, play running | Prototype (M2); provisional |
| Recovery linger | 2 s | Prototype (M1); provisional |
| Restart offer hold | 5 s after the struggle condition clears | Provisional; a decision window, separate from the recovery linger |
| Minimum drag | 100 world px, from the grab point | Baseline |
| Spark size | 12–16 CSS px, fixed screen size | Provisional; check against the real backgrounds |
| Grey opacity | 40% | Provisional; check against the real backgrounds |
| Snap-back time | About 250 ms | Provisional |
| Meter pulse | 2 pulses over about 600 ms | Provisional |
| Refusal line linger | About 2.5 s | Provisional |
| Touch padding | 12 CSS px beyond the drawn edge | Provisional; crowded-cell touch test |
| Touch floor | 44 × 44 CSS px on screen | Provisional; common accessibility guidance |
| First, second and repeat nudge | 30 s, 60 s, 90 s (gaps) | Prototype (A3); provisional |
| Repair jobs per DNA repair enzyme | 5 | **Experimental** (the reuse Deviation, [Specify Level 3 — Membrane](../tickets.md#ticket-64)). One shared value; no per-level lifetimes. Final only after the Level 6–8 infester comparison. |
| Job interval (DNA repair) | 60 frames (Baseline frames at 30 Hz) between one enzyme's jobs while the nucleus stays damaged | **Provisional** (the reuse Deviation, [Specify Level 3 — Membrane](../tickets.md#ticket-64)); about one Baseline enzyme's travel time, so one enzyme's healing rate stays near the Baseline's per-enzyme cadence. One shared value, checked with the repair jobs in the infester comparison. |

## What playtests check

- whether students read the spark and recover from refusals (repeated-refusal counts)
- whether students read the dashed stub
- whether holds that freeze mid-pull look broken
- touch selection in a crowded cell, on a real touch device
- whether the watchdog's hints help
- the linger durations
