# Level spec template

Every level spec (`docs/levels/level-N.md`) uses this shape. It was adopted in [Prototype Level 1 from its spec on the Bake-off build](../tickets.md#ticket-77), after a builder working from the Level 1 spec had to make about 20 guesses and hit 2 logic errors.

The rule behind the shape: **the build contract comes first, and the rationale comes last.** A builder reads the sections in order and stops before the appendix. A reviewer reads everything.

[`level-1.md`](level-1.md) is the first spec written this way.

## Rules for every spec

- **Decide at Level 1's depth.** No Level 1 decision needed more design detail. What was missing was numbers and shared defaults.
- **Don't repeat the shared defaults.** Numbers and UI rules that every level shares live in one place: [`shared-defaults.md`](shared-defaults.md), with the shared systems listed in [`infrastructure-index.md`](infrastructure-index.md). A spec points to them and states only where the level differs.
- **Every number lives in the Tuning table.** Prose names the Tuning entry ("the need-rule cooldown") instead of repeating its value, so a number can't drift between two places.
- **No local copies of shared machinery.** If the level needs something a shared system lacks, the spec names it as an **extension to that shared system**, and the extension gets its own ticket. The spec never describes a level-private version (a "state machine at home").
- **Tune for the typical student.** That's a student within about ±1 standard deviation, who follows the instructions imperfectly but as best they can. Edge cases (frugal, willfully bad, edge-seeking, idle) only have to stay winnable, or reach the level's defined loss. The level is never rebalanced to make them fast or slow.
- **No free resources beyond the level's recipe after Level 1.** That means no free glucose or anything else beyond the level's Baseline pools and scripted spawns. Level 1's rescues and its struggle offer are tutorial help and are **not inherited**. From Level 2 on, students "just have to get good".
- **The lesson must not depend on reading.** Every essential idea is also *shown*: an on-rails demonstration, a meter, a tag or a cue. Many students skip dialogue, and second-language readers take longer.

## Sections, in order

### 1. Summary

A short table:
- the tier and the Learning Targets touched
- the Objective count and the waves
- the win and the loss
- the Dead End status
- the Level Rating
- the **in-level time target**

### 2. Shared infrastructure used

One line per shared system the level relies on, taken from [`infrastructure-index.md`](infrastructure-index.md) with its link, plus anything level-specific about how it is used. At least check:
- the Game Shell state machine
- Objectives and the minimum beat
- Story Hold
- Game Events
- Loss Routes and Starvation
- win-impossible predicates and Dead Ends
- the Restart button
- the idle watchdog
- the Tuning table
- the Level Rating
- the Session Log
- the pseudopod spark preview and refusal
- the adapters

Also check the index's second list (selection and touch targets, the world edge, left-behind gems, persistence, Balance Runs) when the level touches them.

Name every **extension** the level needs here, with its ticket.

### 3. Starting state

A table: resources, units, the world (size, background, edge; the machinery is shared, see [the world edge](shared-defaults.md#the-world-edge)), the membrane, the camera, the HUD, pools, and which producers run.

### 4. Beats

**One table**, one row per beat:

| Story Hold | Objective | Completes when (a state predicate) | Switches on | Movement | HUD reveals | Watchdog progress and highlight |
|---|---|---|---|---|---|---|

The last column gives each Objective's **progress condition** and **semantic highlight target** for the idle watchdog (an organelle, an action, a meter, the nearest resource of a kind, or none; [shared defaults](shared-defaults.md#the-idle-watchdog)).

After the table, add only what the shared Objective and Story Hold rules don't already cover.

### 5. Scripted spawns and rules

Each rule is written as:
- **trigger:** a predicate over state, and the Objectives between which it is armed
- **effect**
- **cooldown**
- **limits:** for example, one at a time, or whether it draws on a pool
- **left behind:** for every scripted or rescue gem, *vanish* or *credit a named pool by a stated amount*; there is no default ([shared defaults](shared-defaults.md#gems-not-drawn-from-a-pool))

### 6. Tuning

The only home for the level's numbers. Each row gives the name, the value, the source (Baseline §, Deviation, or provisional), and how it is checked. Shared values live in the [shared Tuning table](shared-defaults.md#tuning); a level overrides one only by naming it and giving a reason.

### 7. Win and loss

- the win predicate
- the Loss Routes
- the win-impossible predicates, each with a concrete, reachable example
- the Dead End status
- the results screen (play time, never a best time; [shared defaults](shared-defaults.md#clocks-and-the-results-screen))

### 8. Verification

- **Time targets, with the clock and the population named:**
  - the *human* in-level time (from the first Story Hold to the results, measured in real time, checked by playtest)
  - the *bot* regression band (in play time, checked by Balance Runs, calibrated from the bots, with no teaching meaning of its own)
  - the clocks are defined in [shared defaults](shared-defaults.md#clocks-and-the-results-screen)
- **The resource-band table (required).** One row for each resource range crossed with each holding state (for example, ATP below the Starvation Line; from the Line up to the cost of reaching the nearest resource; above that) × (holding fuel, or not). Each row names the rule that moves a student out, or the loss that ends the level. **A row with no answer is a spec bug.**
- The Balance Run scripts, each asserting its outcome.
- Unit tests, including the transitions just above and just below every threshold.
- Defect Patches: which test must fail for each one.

### Appendix

- Deviations, each with its Pedagogy Rationale
- the fork defects the level touches (a one-line list for the untouched ones)
- proofs (no Dead End, recoverability)
- sources and history
