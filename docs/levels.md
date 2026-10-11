# The v1 level list

This file resolves [Reconcile the Baseline into the v1 level list](tickets.md#ticket-13). Terms follow `CONTEXT.md`.

## Sources

- **The Baseline:** `docs/research/baseline-mechanics.md` (fork mechanics and numbers) and `docs/baseline/walkthrough/` (flow and feel).
- **The learning targets:** `docs/learning-targets.md`.
- **Cross-checks made for this ticket (2026-09-28):**
  - The Godot attempt's `levels_old` XML is byte-identical to the fork's level files, so it adds nothing.
  - The Godot JSON levels are a rework and a stub. They are not Baseline and were not used.
  - A full read of the fork source and the walkthrough found which draft-AC features have any footing (see *Ruled out* below).

## The class frame

- **Period.** One 70-minute class period.
- **Class Minimum.** Reached the first time a chloroplast is absorbed (early in Level 5) and its introduction is acknowledged.
  - From then on, the level-select screen shows the Recap's copy button, which students use to paste into Canvas. The later "submit to the instructor's sheet" button will appear at the same point.
  - The Class Minimum is saved with progress and never lost.
  - The Recap asks only about what every student has seen by the Class Minimum. That covers every question on the instructor's earlier Google Form: ATP as money; glucose as fuel, 2 vs 38 ATP; membrane from fatty acids; surplus glucose becoming fat; the nucleus holding DNA; RNA as a blueprint that goes to a ribosome; slicers being protein; the ER making membrane; lysosomes recycling; and the chloroplast making glucose, from its introduction.
- **Class expectation.** Students are expected to *complete* Level 5.
- **Time budget.** For a typical student who is focused, including one restart:

  | Point | Minutes after starting Level 1 |
  |---|---|
  | Class Minimum | ≤ 45 |
  | Level 5 complete | ≤ 55 |
  | Recap and hand-in | ≤ 60 |

  - Attendance and the bell ringer take about the first 10 minutes of the 70-minute period, which leaves about 60 minutes. The budget uses all of it; there is no slack, so a slow Level 5 is where the class plan gives first.
  - The times are **classroom time**, including tutorials, cutscenes and reading during Story Holds, so only human playtests can confirm them.
  - Balance Runs check only the gameplay part (each level's time-to-win range). The per-level specs add a Story Hold reading-time allowance to reconcile the two.
  - A level that blows the budget is flagged for a Tuning change and is never quietly cut.
- **The targets are for scheduling, not deadlines** (teacher, in [Specify Level 4 — Golgi and lysosomes](tickets.md#ticket-65)). In some years the lesson has run into a second class period for students who wanted to finish, while students who stopped at Level 5 got flexible work time.
  - Aim for most students to reach the Class Minimum during the first period.
  - Interested students may continue into another period. Reaching Level 5 is the established stopping point.
  - Playtests report completion times and stuck points. The per-level ranges are provisional estimates, never pass/fail criteria. If a level runs long, investigate avoidable delay first (confusing controls, repeated explanation). Time spent on meaningful choices or a hard wave doesn't automatically need shortening.
- **Optional challenge.** Levels 6–8 are optional. Levels are not labelled differently. When the Class Minimum is reached, no line plays in the level. Instead, the level-select page shows the Recap option, and the teacher reminds the class aloud (decided in [Story and cast](tickets.md#ticket-12), `docs/story.md`). Many students found the original satisfyingly hard at this stage, which is the point.

| Level | Class tier |
|---|---|
| 1–4 | Required |
| 5 | Required to the chloroplast (Class Minimum); expected to complete |
| 6–8 | Optional challenge |

## Rules for every level

The shared numbers and UI rules (lingers, touch targets, the spark preview, Restart, clocks, Pause, the idle watchdog, the world edge) are in [`docs/levels/shared-defaults.md`](levels/shared-defaults.md), and the shared systems are indexed in [`docs/levels/infrastructure-index.md`](levels/infrastructure-index.md).

### Order, count and names

- All 8 Baseline levels, in Baseline order. The organelles arrive in an order that builds LT1 by the end of Level 3 and LT2 by the end of Level 5.
- **Names are our own.** The Baseline's level names are in-game text, so the clean-room rule rules them out. This file uses plain working titles.
  - The names players see are in `docs/story.md`: Wiggle Room, Gesundheit, Room to Grow, Cleanup Crew, Lock In, Lights Out, Cold Feet, Spit Take.
  - A name may not misuse a biology term. The Baseline's Level 7 is named for heat shock but is a cold level.
  - The audience is moving from Gen Z to Gen Alpha. The teacher suggests a "lock in" reference somewhere.

### How a level ends

A level is won when its milestones are complete and its final Objective is met. Each level's final Objective is listed below.

A level is lost in any of these ways:

| Loss | Source |
|---|---|
| Membrane at 0 (lysis) | Baseline (fork and walkthrough) |
| Nucleus at 0 | Baseline (fork and walkthrough) |
| **ATP starvation**: out of ATP for too long | Baseline. The commercial game's Level 3 tutorial names it as a way to die, and the teacher confirms it was a real loss. The fork doesn't implement it, so the details are **reconstruction decisions**, set out in *The shared end-of-level rules* below. Its countdown is on screen, and **the first time the warning appears in any level, a short explanation appears with it.** That matters because starvation can happen in Level 2, before Level 3's tutorial card. Starvation is off in Level 1 (`docs/levels/level-1.md`), so the first warning, and its card, can come no earlier than Level 2. |
| Level 7 only: temperature reaches 0 °C | Baseline |
| **Dead End**: winning is impossible and nothing left can end the level (a draw) | Deviation, already decided (see `CONTEXT.md`) |

**Winning is impossible, but something could still end the level** (waves still to come, cold still falling, starvation under way):
- No forced loss and no modal. The student may play on and let the viruses finish the job, like a chess player who won't resign. Playing on teaches them the wave order for the next attempt.
- A **Restart button** appears under the Objective that can no longer be met, in the Objective pane. With it, a short line says what broke (for example, "no working mitochondrion").
- **Trouble is not impossibility.** A missing organelle is not enough evidence on its own. Recovery routes must be ruled out before an Objective is declared impossible: stored ATP and fat, recycling, existing defences, and the cytoplasm making ATP again once no mitochondrion is left. Each level spec states its predicates with a concrete, reachable example.
  - The button is the teacher's recollection of the commercial game. It is not in the walkthrough footage or the fork.
  - Restart is also always in the Pause menu.
- **Deviation.** This replaces the fork's blocking "Retry / Play on" dialog for a cell with no working mitochondrion or chloroplast. Pedagogy Rationale: *the student still sees what went wrong when an organelle was missing (LT2), without being pushed to quit a fight they can learn from.*

Each level's spec declares its **win-impossible predicates**, each with a concrete, reachable example. The core decides a Dead End from them and the shared Loss Routes (below). Balance Runs and unit tests prove both.

### The shared end-of-level rules

Resolved in [Define the shared end-of-level rules: ATP starvation, Dead End and the Restart button](tickets.md#ticket-60). Every number here is **Tuning**, set provisionally and confirmed by playtest.

#### ATP Starvation

The Baseline's only automatic ATP drain is membrane self-repair (25 ATP per HP every ~2 s, only while damaged). Purchases are all-or-nothing, so a stuck cell usually sits at a few ATP, not exactly 0. Production comes in bursts: 38 ATP per mitochondrion Production Cycle (about 3.2 s: 97 Baseline frames, [verified](tickets.md#ticket-85)), 2 ATP per cytoplasm cycle (~2 s), and slower when a mitochondrion waits on a chloroplast in dim light (~5 s at 20% sunlight, ~10 s at 10%).

- **Starvation Line: 10 ATP.** ATP **below 10** starts the grace. ATP of **10 or more** resets everything, fully. The full reset is a forgiving game rule, not a claim about biology (real cells need production that meets demand, not just any production).
- **Grace: 3 s.** Nothing shows until ATP has stayed below the line for the grace. Balance Runs cover Level 6 at 20% sunlight and Level 8 at 10%, where slow production can outlast it, and report the warning's transitions and total visible time. Playtests judge whether it reads well.
- **Countdown: 60 s.** A provisional reconstruction: the original's length is unknown, so the aim is not to be faster than it, checked against teacher and TA recollection and playtests.
- **What the student sees.**
  - During the countdown: "ATP critically low", with the seconds, on the ATP meter in the warning colour. There is no modal.
  - On recovery, the countdown vanishes at once. An amber "ATP recovering" indicator lingers briefly (Tuning) so a warning that comes and goes each production cycle doesn't flicker. The linger is display only; the rule is unchanged.
  - Story Holds and Pause freeze the grace and the countdown.
  - At 0 the level is lost, with the cause "Your cell ran out of ATP for too long" (working text).
- **The first time in any level** (once per save, saved with progress), the warning opens a short **Story Hold** card, so reading it doesn't use up the countdown. It explains that the cell needs ATP to stay alive and that it dies if ATP stays this low until the timer runs out. The card is then kept in **How to Play** in the Pause menu, like every tutorial card the student has seen.
- **Advice is possible actions, keyed to introductions**, never a guaranteed fix. A small typed table picks lines by what the student has met, keyed to the teaching beat being acknowledged, never to a level number: "Collect glucose if you can reach some" from the start; "Check whether a mitochondrion is switched off" once switching one off has been taught (Level 3's *Switch it off* hold); "Mitochondria can burn stored fat when glucose runs out" once fat has been taught (Level 3's *Fatty acids* hold); "A working mitochondrion is being recycled. Cancel Recycle stops it." once recycling an organelle has been taught (Level 4's *Recycle* hold), shown only while Cancel Recycle is available and cancelling would restore a usable producer (added in [Specify Level 4 — Golgi and lysosomes](tickets.md#ticket-65)). Advice never recommends an action the student can't perform, so Recycle and Divide, which cost more ATP than a starving cell has, are left to the loss hints. Final wording comes with the scripts. (Keying corrected in [Specify Level 3 — Membrane](tickets.md#ticket-64).)

#### Winning is impossible

- Each level spec gives **win-impossible predicates**: typed predicates over core state, each with a one-line reason and a concrete, reachable example.
- **Trouble is not impossibility.** A predicate rules out current inventory *and* what is still coming: scheduled gifts, pools that haven't spawned, queued builds, recycling, stored ATP and fat, organelles that can be switched on, existing defences, and the cytoplasm making ATP again once no mitochondrion is left.
- Predicates are evaluated **after the tick's state changes settle**, so an intermediate state can't trigger them.
- **Irreversible within an attempt.** Once true, a predicate stays true. The core throws in dev and test if one flips back. That guard is cheap but proves nothing: a wrong predicate can stay true forever. The recovery tests are the proof.
- When one fires, the core emits a Game Event, and the **Restart button** appears at once under the Objective it breaks, with the reason line. For example: "No mitochondrion can be switched on, and there's no glucose or fat left to burn." Reasons follow the accuracy rules. There's no sound and no modal. Restart is also always in the Pause menu.
- **The one exception is Level 1,** which can't be lost or reach a Dead End. There, the Restart button is *offered* while ATP is below 10 with no glucose held, and goes away when that clears, even though the level is still winnable (its **struggle condition**, `docs/levels/level-1.md`). Every other level keeps the rule above.

#### Dead End

- **Loss Routes.** One per way to lose: lysis, nucleus at 0, Starvation, 0 °C in Level 7, and any loss a level adds. Each answers **possible**, **impossible** or **unknown** by asking what could still feed it, directly or through another system. Peroxide feeds the nucleus route, and also the Starvation route if it can disable the last working mitochondrion. A wave feeds the routes it can damage, but only if its trigger can still fire: a wave waiting on an impossible Objective feeds nothing. A hazard that can't lead to any loss feeds nothing.
- **Only what happens without player choice counts.** This covers waves, hazards, automatic drains and temperature. Spending ATP on purpose is the student resigning, which is what Restart is for.
- **Order within a tick.** Resolve completed wins and losses first. Then check the win-impossible predicates. Then check the Loss Routes.
- **A Dead End** is: a win-impossible predicate is true, *and every* Loss Route answers **impossible**. Anything **unknown** leaves the student in control, with the Restart button showing.
  - Unknowns are not treated as harmless: time wasted in a 70-minute class matters. They show in the diagnostics overlay and in Balance Run reports, each level spec lists its known unknowns, and each is reviewed deliberately.
- **The Dead End screen** is the ordinary loss screen: "This attempt can't continue", then the predicate's reason, then Restart. The Level Rating works like any loss: the attempt isn't passed, and an earlier best rating is kept.

#### Testing

- **Starvation unit tests:**
  - the line boundary (9 starts the grace, 10 resets)
  - the grace
  - the full reset
  - the countdown vanishing at once while the amber indicator lingers
  - the freeze during a Story Hold and a Pause
  - the once-per-save card
  - advice picked by introductions
- **Per predicate:**
  - a Balance Runner script that *reaches* the spec's example, asserting that the predicate fires (and the Dead End, if the spec claims one)
  - **one near-miss per materially different recovery route**, each asserting that the predicate stays false and that the autopilot goes on to win. A scripted win proves that route; a script that fails to win proves nothing.
- **False-ender tests:** an unreachable future wave and a harmless leftover hazard must each answer impossible, and must not suppress a real Dead End.
- **Defect Patches:**
  - a predicate that ignores stored fat (the near-miss tests must fail)
  - a wave route that ignores whether its trigger can still fire (the false-ender test must fail)
  - a Starvation reset at 9 instead of 10 (the boundary test must fail)
- **The stuck-state search.** The Balance Runner's search (no progress and no possible Loss Route for a Tuning-set time, with no Dead End fired) fails the run and saves the state and trace. It flags a state for investigation; it doesn't prove a bug. The flag clears only by one of these:
  - **A missing or wrong predicate, or a wrong Loss Route:** a fix plus a regression test.
  - **A legitimate long wait:** a narrow exception in the level's fixtures, stating its reason and its expected exit condition or duration.
  - **An autopilot limitation** (the automated player is stuck where a student has a route): a narrow exception naming that route, and ideally a scripted run that takes it.

  Labelling a flag never makes the run pass on its own.

### The Level Rating

Resolved in [Define how wave grades make the Level Rating](tickets.md#ticket-61). Terms are in `CONTEXT.md`. The Baseline's letter grades never appear in the game.

| Rating | Label |
|---|---|
| 1 | Emerging (only a level not yet finished) |
| 2 | Developing |
| 2.5 | Nearly Proficient |
| 3 | Proficient |
| 3.5 | Proficient + |
| 4 | Advanced |

- **Wave Rating.** Given only when a wave is **defeated**. The Baseline's wave score is (viruses spawned inside + viruses escaped) ÷ the wave's starting count, plus nucleus infestation ÷ (5 × the starting count). A score of 0 earns 4; below 1 earns 3.5; below 2 earns 3; below 3 earns 2.5; anything higher earns 2. The wave-defeated card shows it as number and label in place of the Baseline's letter.
  - **Deviation: the floor is 2.** The Baseline has two bottom grades (D and D−), and D− counts for less in its level average. Here both are a 2, inside and out. Pedagogy Rationale: the Level Rating must be traceable from the Wave Ratings the student actually saw, so it reads as feedback, and a cell that survived a wave has passed it. Calling a survived defence the lowest mark was needlessly harsh.
- **Level Rating.** The average of the level's Wave Ratings, **weighted by each wave's starting count** (Baseline). Later waves are bigger, so the rating mostly reflects whether the cell's economy held up late in the level.
- **Rounding** keeps the Baseline's cushion: an average within 0.1 below a step counts as that step. So 3.9 or more is 4; 3.4 or more is 3.5; 2.9 or more is 3; 2.4 or more is 2.5; below that is 2. Examples: the walkthrough's Level 5 (a 2 wave of 10 and a 3.5 wave of 15) averages 2.9 and earns 3, as the Baseline's B; two equal waves rated 2 and 4 earn 3.
- **Only defeated waves count.** A wave still in flight when a meter level (7 or 8) is won is ignored, so finishing just before a poor wave is defeated can rate higher than finishing just after. This is Baseline behaviour, **accepted deliberately as a scoring quirk**; the learning targets don't require it.
- **No defeated waves** (Level 1 always; Level 7 or 8 if won before any wave is defeated): there is **no Level Rating**. The results screen says "Complete!" and "No waves were scored." The save and the Recap show "Complete", never a 4, so a number always means the student defended.
- **Results screen:** "Level complete!", the Level Rating with its label (or "Complete!"), a "new best" marker when the rating beats the saved one, the play time as secondary information (no best time; `docs/levels/shared-defaults.md`, *Clocks and the results screen*), and the note that the rating is only part of what the instructor looks at. There is no per-wave breakdown; the wave cards already showed each one.
- **Saving.** The best Level Rating is kept; best time is not stored ([Define the shared level defaults and the infrastructure index](tickets.md#ticket-79)). On a replay any number beats "Complete", and "Complete" never replaces a number. A lost attempt changes neither.
- **Testing.**
  - Unit tests on every rounding boundary on both sides (3.9 and just under it, and so on), with the comparison done in whole numbers (tenths of a rating × starting count) so floating-point error can't move a student's result.
  - Unit tests on each Wave Rating threshold, the floor, and the "Complete" cases (no waves; a meter win with only waves in flight).
  - Defect Patches: rounding to the nearest half step (a 2.3 average must stay 2, so that test must fail); an unweighted average (the walkthrough Level 5 test must fail); counting an in-flight wave (the meter-win test must fail).

### Deviations that apply to every level

| Deviation | Pedagogy Rationale | Decided in |
|---|---|---|
| The pseudopod preview and cost are clamped to the lens, so the charge matches the movement actually bought | Students read ATP as the price of a visible action; charging for distance the cell never travels makes ATP spending look arbitrary | [Draft the learning targets the game reinforces](tickets.md#ticket-11) |
| "Free radicals" are called **hydrogen peroxide** (or "peroxide") everywhere a player sees them | Students should learn the correct term, and "peroxide" explains what peroxisomes are for | [Draft the learning targets the game reinforces](tickets.md#ticket-11) |
| The Baseline's "NA" (nucleic acids) is called **Nucleotides (NT)** everywhere a player sees it: "Nucleotides (NT)" at its introduction and on the meter, NT plus the icon in compact costs | Nucleotides are the building blocks and nucleic acids are what they make, so labelling the building block "nucleic acids" teaches the wrong level of the hierarchy. No change to costs, quantities or balance. | [Specify Level 2 — Nucleus](tickets.md#ticket-63) |
| Story Hold replaces the faux pause, so no swimming or gathering during beats | See `CONTEXT.md` | [Define the architecture boundaries](tickets.md#ticket-16) |
| Dead End as a loss | See `CONTEXT.md` | this ticket, refined |
| Level Rating on the school's 1–4 rubric | See `CONTEXT.md` | the map |
| Wave Ratings have a floor of 2: the Baseline's D and D− both count as 2 | *The Level Rating* above | [Define how wave grades make the Level Rating](tickets.md#ticket-61) |
| A Restart button under an Objective that can't be met, instead of the blocking "screwed" dialog | Above | this ticket |
| Our own level names; none misuses a biology term | A misleading name teaches a wrong term (Level 7) | this ticket |
| The pseudopod spark preview and refusal cues replace the Baseline's alert | Students read ATP as the price of a visible action, and showing how far the current ATP reaches turns a refusal into the lesson "cost grows with distance" | [Define the shared level defaults and the infrastructure index](tickets.md#ticket-79) |
| No best time is saved or shown; the results screen shows this attempt's play time | Speed is not a learning target. Removing best-time comparisons avoids encouraging rushed play and keeps attention on completing the level and improving defensive strategy | Same |

Accuracy rules 1–10 in `docs/learning-targets.md` bind every level's text. The Baseline's "the cell is adapting" line in Level 5 is one example; Aqua's counter to it is set in `docs/story.md`.

### Fork defects

The catalogue's defects (§F) come in two kinds:
- **Clear implementation failures** with no player-visible intent: a misspelt name, a wrong multiplier that happens to match, dead conditions, gems stacked on one spot, lenient-XML needs. Fixing these is not a Deviation.
- **Behaviour a player can feel, whose intent is uncertain:**
  - D1: resource caps that never apply
  - D2: 100% recycling refunds
  - D3: shrinking the membrane before paying
  - D5: no cap on DNA repair
  - D8 and D9: Level 7's missing wave and fast-returning viruses
  - D13: the fragile Level 1 win
  - D15 and D16: silent pool drain and slower ticks

  Changing any of these can change difficulty. It needs a documented decision (and a Pedagogy Rationale if it's a Deviation from what players saw) and a Balance Run check.

Every defect a level touches gets an explicit decision in that level's spec.

- **D15, for the whole core** (decided in the Level 1 spec): a roll whose spot has no room makes no gem and costs the pool nothing. The fixed-pool levels (5, 6 and 7) check it in their Balance Runs.
- **D16:** the Baseline's effective timings (16 frames, 72 px) are kept as Tuning. The Bake-off core uses the nominal ones and is corrected after the golden traces are recorded.

## The levels

Each level lists what it introduces, what it teaches (tier from `docs/learning-targets.md`), how it's won and lost, its Deviations, and its open items for the per-level spec. The numbers and beat order are the Baseline's, in the catalogue §8.

### Level 1 — Pseudopod (working title)

**Full spec: [`docs/levels/level-1.md`](levels/level-1.md)**, decided in [Specify Level 1 — Pseudopod](tickets.md#ticket-62) and revised after [Prototype Level 1 from its spec on the Bake-off build](tickets.md#ticket-77).

- **Tier:** required.
- **Introduces:** the centrosome (claimed by tapping it), the cytoskeleton and membrane, pseudopod movement that costs ATP, the HUD, glucose broken down in the cytoplasm for ATP, and a mitochondrion that floats in and makes far more ATP from each glucose.
- **Teaches:** LT1 (glucose is fuel for ATP; ATP is the currency) and LT2 (the centrosome and cytoskeleton organise movement; the mitochondrion's job). The 38 ATP disclaimer appears at its first reveal.
- **Won:** ATP ≥ 1000 once Objective 6 is armed, checked every tick. The level starts at 950 ATP.
- **Lost:** never. Starvation is off, so there is no Loss Route, no win-impossible predicate and no Dead End.
- **Level-specific Deviations:**
  - a starting ATP of 950
  - Starvation off
  - two glucose rescues: a gem ahead of a cell with none nearby, and a gem that floats in to a cell below 10 ATP holding no glucose
  - a Restart *offer* while ATP is below 10 and no glucose is held (the one exception to the Restart rule)
  - the ATP meter shown on the first grab of the membrane

  The rescues and the offer are tutorial help that **no later level inherits**.
- **Time:** about 3½ min in the level for a typical student (about 120 s of play and 90 s of reading), so about 4½ min of classroom time with the opening cutscene. No level has a player-facing time limit.

### Level 2 — Nucleus (working title)

**Full spec: [`docs/levels/level-2.md`](levels/level-2.md)**, decided in [Specify Level 2 — Nucleus](tickets.md#ticket-63).

- **Tier:** required.
- **Introduces:** claiming the nucleus; amino acids and nucleotides; selecting and the Action Panel; ribosomes (build to 20); camera zoom and scroll; the cost preview; slicers (build to 10); RNA as a temporary cost; membrane health; the first virus waves (injectors); escaped viruses coming back.
- **Teaches:** LT1 (amino acids are chained into proteins; nucleotides into nucleic acids such as RNA) and LT2 (the nucleus holds DNA and sends RNA instructions; ribosomes build proteins). Foreshadowed: DNA → RNA → ribosome → protein.
- **Won:** by defeating an injector wave of 5, then one of 15.
- **Lost:** membrane at 0 or Starvation. Nothing in this level damages the nucleus. This is the first level with no tutorial help and the first where Starvation can fire.
- **Win-impossible:** one predicate before wave 1 is sent (*Can't finish building up*). No Dead End has been identified.
- **Level-specific Deviations:** amino acids and nucleotides as one checklist Objective; no mitochondrion actions until their introductions; partial purchases reported honestly and counted (D18); the alert-waiting Objective dropped; hover-triggered cost cards replaced.
- **Time:** about 7½ min in the level for a typical student (working range about 7–9 min).
- **Extensions:** [Camera zoom, scroll and scripted moves](tickets.md#ticket-82), [Action Panel purchases](tickets.md#ticket-83) and [Virus wave presentation](tickets.md#ticket-84) (shared interaction contracts); open dependency: [Verify the mitochondrion's production cycle](tickets.md#ticket-85).

### Level 3 — Membrane (working title)

**Full spec: [`docs/levels/level-3.md`](levels/level-3.md)**, decided in [Specify Level 3 — Membrane](tickets.md#ticket-64).

- **Tier:** required. **LT1 is complete by the end of this level.**
- **Introduces:** selecting a group and recycling it (all 50 starting slicers, which opens the glucose and amino-acid supplies); surplus glucose becoming fatty acids; claiming the ER; growing the membrane (3 times) and shrinking it (once); hydrogen peroxide damaging the nucleus (switched on mid-level); moving a mitochondrion and switching it off; DNA repair; peroxisomes (build to 3); the card listing the three ways to die.
- **Teaches:** LT1 (fatty acids are used to make membrane lipids, not chains of them; surplus glucose becomes fat) and LT2 (the ER makes membrane; peroxisomes break down peroxide; DNA repair enzymes mend DNA and work repeatedly). Experienced: recycling returns building blocks but not the ATP spent.
- **Won:** by defeating an injector wave of 15, then one of 25.
- **Lost:** membrane at 0, nucleus at 0, or Starvation. The ways-to-die card lists all three; the first-time Starvation card stays separate.
- **Win-impossible:** one declared predicate (*No peroxide source*), a Dead End when ATP is at or above the Starvation Line; three candidates go to [the validation task](tickets.md#ticket-89).
- **Level-specific Deviations:** a visible "Select all" action with an Objective that requires the whole group; reusable DNA repair enzymes (a shared value, experimental); D3 and D24 fixed; placeholder Objectives replaced by holds keyed to the burst's recorded hit; state-predicate Objectives; actions enabled when their Objective is armed.
- **Time:** about 12 min in the level for a typical student (working range about 11–14 min), provisional.
- **Extensions:** [Group selection and group actions](tickets.md#ticket-87) and [Organelle Move and Toggle](tickets.md#ticket-88) (shared interaction contracts), plus Level 2's three.

### Level 4 — Golgi and lysosomes (working title)

- **Tier:** required.
- **Introduces:**
  - a damaged mitochondrion floating in
  - claiming the Golgi
  - lysosomes (build to 10)
  - recycling the damaged mitochondrion
  - dividing a mitochondrion
  - invader viruses, eaten by lysosomes
- **Teaches:**
  - LT2: the Golgi finishes and ships vesicles; lysosomes digest and recycle damaged organelles and invaders; mitochondria divide.
- **Won:** by defeating an invader wave of 5, then the final attack: a wave of 10 invaders and a wave of 15 injectors, sent together.
- **Lost:** membrane at 0, nucleus at 0, or starvation.
- **Level-specific Deviations:** sequencing and presentation adaptations only (Objective 8 armed at the send; state predicates; the completion linger; the digestion vesicle's wait shown as progress; a Cancel Recycle advice line). The wrong-mitochondrion freedom and Divide without a health check are Baseline, kept on purpose.
- **Spec:** [`docs/levels/level-4.md`](levels/level-4.md) (Cleanup Crew), decided in [Specify Level 4 — Golgi and lysosomes](tickets.md#ticket-65). It settles the unsent waves (not declared), the menus rebuilt at load (declared in content, peroxisome Move included), and the lysosome cap (25; the walkthrough's 16 was a made/ordered display).

### Level 5 — Chloroplast (working title)

**Full spec: [`docs/levels/level-5.md`](levels/level-5.md)** (Lock In), decided in [Specify Level 5 — Chloroplast](tickets.md#ticket-66).

- **Tier:** required up to the Class Minimum; expected to be completed. **LT2 is complete by the end of this level.**
- **Introduces:** a chloroplast floating in when the opening hold closes (about 26–28 s, sooner if the student swims to it); light energy used to build glucose, and more peroxide as a result; dividing a chloroplast; auto-replacing peroxisomes; fat building up; defensins (build to 5 fused), diluted by a bigger membrane; vesicle-wrapped viruses, which get past them.
- **Class Minimum:** reached when the chloroplast's introduction hold is acknowledged; saved at once and never lost ([Persistence extension: save the Class Minimum](tickets.md#ticket-101)).
- **Teaches:** LT2 (the chloroplast uses light energy to build glucose from carbon dioxide and water, accuracy rule 10); LT1 (defensins are proteins); organelle origin (the endosymbiosis small print).
- **Won:** by defeating an invader wave of 10, then one of 15 (7 wrapped).
- **Lost:** membrane at 0, nucleus at 0, or Starvation.
- **Win-impossible:** one declared predicate (*No chloroplast left to divide*, replacing the Baseline's "no working chloroplast" dialog), usually a Restart offer; two candidates go to [the validation task](tickets.md#ticket-99).
- **Level-specific Deviations:** presentation and sequencing only: an honest, re-arming ATP tip (only while a working mitochondrion is switched off); an illustrative sunlight demonstration (play stays at 100%); missed auto-replacements reported; the shield labelled as the chance to block an unwrapped virus; counted and state-predicate Objectives. D25 fixed; the Baseline's freedom to recycle the only chloroplast kept.
- **Flagged for balance.** It gets hard for some students, and the class expects them to finish it.
  - Any Tuning change it gets carries this Pedagogy Rationale: *the class expects every student to complete this level within one period, so a wall here costs students the chance to use the chloroplast they just earned.*
  - Every change is confirmed by human playtest as well as Balance Runs. The spec ships the Baseline numbers and pre-approves no change: presentation first, then whichever mechanical lever the observed problem supports.
  - **Organelle Repair** (a dormant fork action) is noted as a last-resort, drastic lever, and only with a strong pedagogy reason.
- **Time:** about 11 min in the level for a typical student (working range about 10–14 min), a scheduling estimate. Open dependency: [Verify the chloroplast's production cycle](tickets.md#ticket-100).

### Level 6 — Journey (working title)

- **Tier:** optional challenge.
- **Introduces:**
  - a survival gauntlet with no glucose supply (ATP is set to 1000 at the start)
  - sunlight dipping to 20% and recovering
  - **infesters**, which infect the nucleus and are cleared by DNA repair
- **Teaches:** LT2 applied. The chloroplast feeds the cell when there is no glucose, and DNA repair protects the nucleus.
- **Won:** by defeating the last of six escalating wave steps: after a 60 s countdown, injectors, then invaders, then infesters, then three mixed trios (the last two partly vesicle-wrapped).
- **Lost:** membrane at 0, nucleus at 0, or starvation.
- **Level-specific Deviations:** none.
- **For the spec:**
  - The **strongest win-impossible risk** (not a Dead End). Glucose comes only from the chloroplast, and fatty acids can't be gathered, so losing the chloroplast can leave the cell unable to fight on. But the level is won by clearing waves, and undefeated waves (including escapees that come back) can still end it. So this is a "play on, Restart button" case, not a draw. The spec must check recovery routes before calling it impossible, and must give a concrete, reachable Dead End example or show that none exists.
  - Only the invaders and infesters get the 10% and 20% vesicle wrap.
  - Infesters arrive with no countdown.

### Level 7 — Cold (working title)

- **Tier:** optional challenge.
- **Introduces:**
  - a cold world: it starts at 8 °C and loses about 1 °C every 15 s
  - the mitochondrion burning glucose for heat instead of ATP (brown fat)
- **Teaches:** LT2 (the mitochondrion can make heat instead of ATP).
- **Won:** by reaching 25 °C.
- **Lost:** 0 °C, membrane at 0, nucleus at 0, or starvation.
- **Level-specific Deviations:** its name. The Baseline's calls it a heat-shock level; ours will not.
- **For the spec:**
  - The glucose supply is a fixed pool of 500 that never regrows.
  - Defect D8: the undefined fourth wave and objective. The walkthrough shows a countdown with no wave, so the proposal is to drop them.
  - Defect D9: escaped viruses come back after about 1 s instead of 15.

### Level 8 — Toxin (working title)

- **Tier:** optional challenge.
- **Introduces:** the Golgi making toxin, filling the toxin meter while under timed attack.
- **Teaches:** LT2 (the Golgi ships products out of the cell).
- **Won:** by filling the toxin meter to 100.
- **Lost:** membrane at 0, nucleus at 0, or starvation. **A Dead End is possible.**
  - The parallel timer chain is finite: 11 sends over about 17 minutes, then nothing more.
  - So once every wave has been sent *and defeated* (escaped viruses come back until their wave is beaten), a cell that can't reach 100 toxin, and isn't starving, is at a Dead End. The spec must show a concrete, reachable example, or show that none exists.
- **Level-specific Deviations:** none.
- **For the spec:**
  - At toxin 10 and 25 the level sends a pair, not a trio. At 75 it sends a single wave. At 5 nothing is sent.
  - The start units: the fork has 2 mitochondria and 2 chloroplasts, while the video shows 1 of each. The fork rules on numbers.
  - The "digestive fluids leaking in" line is story only. There is no pH mechanic.
- **After this level:** the finale cutscene, now specified in `docs/story.md`. The original idea, for reference: The cell is safe and starts dividing and specialising into the first Tardigrade settler, expecting to be its usual size and rule the planet. It turns out to be microscopic, next to a giant water droplet on a blade of grass, though very indestructible. It's a cutscene, not a mechanic, and not made in the game engine. It must use no evolution language.

## Ruled out of v1

Most of the superseded draft AC doc's features have **no footing** in the fork or the walkthrough: heat shock proteins, Viral Protease, temperature effects on play, cold-adapted infesters, centrosome upgrades, membrane-tension penalties, antibodies, a cell wall and thylakoids. Some memories trace back to real Baseline things, which v1 keeps:

- **Vesicle-wrapped viruses** (the "enveloped" ones), in Levels 5, 6 and 8.
- **Lysosome acid inside vesicles**, used by every recycle.
- **Organelle "Divide"** (the "fission"), in Levels 4 and 5.

The fork also has actions that are built but never offered in any level. **None comes into v1:**

- the lysosome "pop" (acid dumped into the cytoplasm)
- player Necrosis and Apoptosis, which are self-harm actions
- a basal body (cilia or flagellum), which would be a second way to move
- **cell mitosis** and its "daughter cells" counter, which do nothing in the Baseline. Cell division lives only in the finale joke.

Organelle Repair stays dormant. It is available only as the Level 5 lever above.
