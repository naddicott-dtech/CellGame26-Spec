# CellGame26

A web-native, MIT-licensed reimagining of the abandoned open-source CellCraft fork, used as a 5E "Engage" activity for d.tech biology students. Its explicit teaching scope is biochemistry (monomers and the macromolecules built from them) and cell biology (organelles and what they do).

## Language

### Project

**Baseline**:
The combined picture of the original game that CellGame26 starts from: the open-source fork's source code (mechanics, numbers, lose conditions — catalogued in `docs/research/baseline-mechanics.md`) together with the walkthrough observations of the commercial game (player-visible flow and feel — `docs/baseline/walkthrough/`). The draft acceptance-criteria doc is superseded and is not a Baseline source.
_Avoid_: Original, source of truth, spec

**Deviation**:
Any deliberate difference between CellGame26 and the Baseline that a player can see or feel — mechanics, numbers, flow, look and feel. A different internal model that reproduces the same player-visible behaviour is not a Deviation. Every Deviation carries a Pedagogy Rationale; convenience is never a valid reason.
_Avoid_: Change, simplification, shortcut

**Pedagogy Rationale**:
The one-line teaching reason that justifies a Deviation, written so a reviewer can check it.

**Bake-off**:
The head-to-head comparison of candidate rendering stacks, each built as a thin layer over the same shared simulation core, judged by a Scorecard.

**Simulation Core**:
The renderer-agnostic game logic — cell, membrane, pickups, resources, metabolism, movement — that every Bake-off contender (and later the real game) draws from. Two renderers on the same Simulation Core must never behave differently.
_Avoid_: Engine, backend, model

**Bake-off Slice**:
The small piece of gameplay every Bake-off contender builds: Level 1's core loop (swim by pseudopod, collect glucose, earn and spend ATP) with only enough HUD to see it working. It tests whether moving the cell feels good and whether the membrane misbehaves; it does not set the art style.
_Avoid_: Demo, MVP, vertical slice

**Stress Scene**:
A fixed, reproducible situation every contender must survive, chosen to break membranes or frame rates: a long pseudopod, a sharp turn, an overcrowded cell, and a heavy on-screen load.

**Process Log**:
A contender's append-only record, kept while building, of every pain point the building agent hit and what it cost.
_Avoid_: Journal, diary

**Scorecard**:
The fixed set of criteria every Bake-off contender is examined on (feel, tab performance and memory, testability, agent friction, load size, how well it renders a deformable membrane, code quality), each backed by evidence: measurements, the Process Log, and the teacher's playtest impressions. It informs a discussion that ends in consensus; it is not a weighted formula that picks the winner.

**Defect Patch**:
A deliberate, named break in the game, paired with the tests that must catch it. It proves those tests can fail. It is only ever applied to a throwaway copy of the code, never to the copy anyone plays or ships.
_Avoid_: Mutant, sabotage, broken build

**PR Gate**:
The checks every pull request must pass before it can merge, run automatically on every change. A green PR Gate always means the same set of checks passed.
_Avoid_: CI, pipeline, smoke test

**Thorough Check**:
The deeper, slower checks a risky change must also pass before it merges, chosen by what the change touches and run on the Mac, with the results attached to the pull request. The full set also runs before every classroom release.
_Avoid_: Nightly, full suite, extended tests

**Build Slice**:
One build ticket: a coherent piece of the game that produces something meaningful to verify, executable by an agent without inventing product behaviour. Implementation choices (names, signatures, module layout) stay the builder's. One Build Slice may land as several PRs.
_Avoid_: Task, story, module (a list of modules is not a list of slices)

**Playable Level**:
A level whose complete specified behaviour runs on the production architecture, verified in the browser and ready for human playtesting, with its deferred features named. Its scope may be limited; the quality of what it implements is not. A Playable Level is not yet validated for classroom use: that also needs its validation task, the release checks and human playtests.
_Avoid_: Demo, prototype, vertical slice

### Game

**Game Shell**:
The part of the game that decides which screen the player is on: title, level select, cutscene, a level in play, results. It starts a fresh level each time, and it restores the player's progress from their **Progress Save**. The Baseline calls it the Director.
_Avoid_: Director, game manager, app

**Pause**:
Everything stops: no gameplay, no animation-driven change, no timers. The player causes it (pause button, opening a menu) or the browser does (the tab is hidden). A level never pauses itself.
_Avoid_: Story Hold, freeze

**Story Hold**:
A level stops the player's part of the game so a story or tutorial beat can play. Resources, waves, damage and timers stop, and the player can't swim or gather. Only what the beat is showing moves: scripted camera moves, idle animations, and an on-rails demonstration of the new thing being taught. A Pause during a Story Hold stops that too. The Baseline's "faux pause" let players keep swimming and gathering; that exploit is a Deviation we remove, so no level can depend on it.
_Avoid_: Faux pause, pause

**Cutscene**:
A short sequence of comic panels between levels (character sprites composed over a backdrop, with live text) that carries the story forward. Students advance it at their own pace and can skip it. Story told inside a level is dialogue during a Story Hold, not a Cutscene.
_Avoid_: Movie, cinematic

**Game Event**:
Something that happened in a level that a player could notice or that a teacher could care about: resources gained or spent, an organelle built, an objective completed, a Field Note answered, a level won or lost. Game Events happen in one fixed, reproducible order, and several parts of the game react to each of them.
_Avoid_: Signal, message, telemetry

**Objective**:
The one current goal of a level, always visible to the player. Only one Objective is active at a time. It can't be completed until its introduction has been acknowledged, and each completion stays on screen briefly before the next Objective begins, so no step is skipped unseen.
_Avoid_: Quest, task, mission

**Dead End**:
A level state in which winning has become impossible *and* every Loss Route is impossible, so nothing left in the level can end it. It is the game's draw. A Dead End is a loss, shown with a restart screen ("This attempt can't continue", plus what broke), never left for the player to wander in. While any Loss Route is possible or unknown, an unwinnable level is not a Dead End: the student may play on, like a chess player who won't resign, and learn the attack order for the next try, with a Restart button showing. The Baseline has no such loss, so this is a Deviation (Pedagogy Rationale: a student stranded in an unwinnable level learns nothing and disengages).
_Avoid_: Soft lock, stuck, draw screen

**Loss Route**:
One way a level can still be lost without the player choosing it: lysis, nucleus at 0, Starvation, freezing in the cold level, or a loss a level adds. Each answers *possible*, *impossible* or *unknown*, taking in everything that could still feed it, directly or through another system (peroxide that disables the last mitochondrion feeds Starvation). Spending ATP on purpose is the player resigning, not a Loss Route.
_Avoid_: Ender, hazard, threat

**Struggle condition**:
A state in which a level offers the Restart button even though it can still be won. It exists only in Level 1 (ATP below 10 with no glucose held), where the level can't be lost; every other level shows Restart only when winning is impossible. The offer disappears when the state clears.
_Avoid_: Soft fail, low-ATP warning

**Idle watchdog**:
The rule that notices when a student has made no progress on the current Objective for a while and nudges them back to it with a short, non-blocking hint. It never ends a level.
_Avoid_: Timeout, AFK check

**Spark Preview**:
While the player drags a pseudopod, the aiming arrow shows how far the current ATP reaches: the ordinary colour where a release is affordable, grey where it isn't, and a red spark at the point where it stops being affordable. It is information about a cost, not help, so it applies in every level.
_Avoid_: Range indicator, cost arrow

**Refusal**:
The game declining a player's action and saying why: a pull their ATP can't pay for (nothing moves and nothing is spent, the Baseline's all-or-nothing rule), or a purchase that buys nothing (can't afford, at the maximum, no free pore). Every refusal shares one tone and look: the limiting meter pulses and a short line names the cause and the fix; a pull's arrow also snaps back to the spark. Each keeps its own action and reason, so a pull's behaviour never carries over to a purchase. A refusal must read as intentional, never as a bug.
_Avoid_: Error, failed pull, failed purchase, cancel

**Partial Fill**:
A batch purchase that bought some of the units asked for but not all, because a unit failed partway (for example, "Bought 3 of 5 ribosomes: not enough amino acids for the rest"). It is not a Refusal: the units bought are charged and count as a purchase. It reports how many were asked for, how many were bought, and why the rest weren't.
_Avoid_: Failed batch, partial refusal

**Linger**:
A short display that stays after the thing it reports has already happened, so it isn't missed or doesn't flicker: the **Completion Linger** (a finished Objective staying on screen before the next begins) and the **Recovery Linger** ("ATP recovering" after a Starvation countdown vanishes). A linger only displays; it never delays the rule behind it.
_Avoid_: Delay, cooldown

**Skip to gameplay**:
On a Restart or a replay, passing Story Holds the student has already seen with one tap: the named hold closes, then each already-seen hold that opens right behind it, stopping at an unseen hold or at gameplay that needs an action. Each closes as Continue would close it, every effect exactly once, and no gameplay runs. A hold not yet seen can't be skipped. (It replaced *Fast-forward*, which only finished a seen hold's demonstration.)
_Avoid_: Fast-forward (retired); a bare "Skip", which doesn't say where it goes

**Play Time**:
The time a student spent actually playing a level: gameplay outside Story Holds and Pause. It is shown quietly on the results screen and never as a running clock or a best time. In-level time (holds included) is a separate measure, used for playtests.
_Avoid_: Score time, best time, level timer

**Starvation**:
Losing a level because the cell's ATP stayed critically low for too long. "Critically low" means below the Starvation Line, a Tuning number; after a short grace a countdown shows, and climbing back to the line cancels it completely. The Baseline's commercial game names it as a way to die but the fork never built it, so its details are a reconstruction.
_Avoid_: Out of ATP, energy death, ATP death

**How to Play**:
The list in the Pause menu of every tutorial card the student has seen so far, so an explanation shown once can be read again.
_Avoid_: Help, manual, tutorial history

**Level Rating**:
The score a student earns for a level they finished, on the school's 1–4 rubric: 2 Developing, 2.5 Nearly Proficient, 3 Proficient, 3.5 Proficient +, 4 Advanced; 1 Emerging only ever stands for a level not yet finished. It is the Wave Ratings of the level's defeated waves, averaged with bigger waves counting for more. A finish with no defeated waves has no Level Rating; it is simply "Complete". The results screen notes that it is only part of what the instructor considers. The Baseline grades with letters (its "Defense Rating"); using the school's rubric is a Deviation (Pedagogy Rationale: students already know this scale and what each level means, so the rating reads as feedback, not a verdict).
_Avoid_: Grade, score, Defense Rating, GPA

**Wave Rating**:
How well the cell handled one defeated wave, on the same rubric (2 to 4), judged by how many viruses got inside, escaped, or infested the nucleus. Surviving a wave always earns at least a 2. It is shown on the card that appears when the wave is defeated, and it is what the Level Rating is built from.
_Avoid_: Wave grade, performance grade

**Nucleotides (NT)**:
The building-block resource the nucleus uses to make RNA, shown as "Nucleotides (NT)" when introduced and on its meter, and as NT plus its icon in compact costs. NT is the game's own shorthand, not formal scientific notation. The Baseline labels this resource "NA" (nucleic acids), which names the chain rather than its building block; calling it nucleotides is a Deviation for accuracy.
_Avoid_: NA, nucleic acids (for the resource)

**Action Panel**:
The panel docked at the bottom of the screen that shows the selected organelle's name, health, one-line description, encyclopedia link and the actions it can take now. Its contents depend on which organelle is selected.
_Avoid_: Organelle menu, build menu

**Focus Effect**:
A short procedural animation that draws the eye to a moment the art doesn't cover, such as a ribosome leaving the nucleus through its pore. It's presentation only, has a fixed duration and capped concurrency, and resembles the item's final form (`docs/architecture.md`, *Renderer*).
_Avoid_: Juice, polish effect

**Tuning**:
The numbers that set how a level plays: costs, rewards, spawn amounts, wave sizes and timings. They start from the Baseline. Any change a player could feel is a Deviation, and a big change to waves or timing needs a human playtest as well as automated runs.
_Avoid_: Config, settings, params

**Production Cycle**:
How often a producer (a mitochondrion, the cytoplasm, a chloroplast) delivers its output: the time between one credit and the next, while it keeps producing uninterrupted with enough input. It says nothing about what happens inside the interval; each producer's phases are its own.
_Avoid_: Cycle (unqualified), tick, burn rate

**Balance Run**:
Playing a level automatically, without a screen, many times over, to see how Tuning changes affect things like time to win, resources over time, and losses. It points out problems; it doesn't decide whether a level is balanced for students.
_Avoid_: Simulation, benchmark

### Classroom

**Learning Target**:
One of the two things a student should be able to notice and describe after play: monomers make useful things, and organelles do real work. Each topic the game touches has a tier. A Learning Target may be asked about at normal difficulty. A Foreshadowed topic (shown in play, taught properly in a later unit) may be asked about only at the easiest level. An Experienced topic (felt in play, never taught as a fact) is never asked about.
_Avoid_: Objective, standard, outcome

**Field Notes**:
Short in-game questions placed at story beats that check what a student noticed. Answers are recorded but never block progress. They are sparse: at most one per level, and not every level has one.
_Avoid_: Quiz, worksheet, assessment

**Recap**:
The "were you paying attention?" questions a student answers after play, gathered with their progress into a report they copy and hand in themselves. It replaces the instructor's Google Form; the game sends nothing anywhere.
_Avoid_: Quiz, test, form

**Class Minimum**:
The point a student must reach in class: their cell has absorbed its first chloroplast, and they have acknowledged its introduction. Once reached, it is never lost, and the student can hand in their Recap. The class goal is to finish that level too; everything after it is optional challenge. The Recap asks only about what a student has seen by the Class Minimum.
_Avoid_: Checkpoint, required level, done

**Progress Save**:
The one versioned record of how far a student has come, kept on the device: the levels they've completed with their best Level Rating, the teaching they've already acknowledged, and the once-per-save cards they've passed. It's what unlocks levels and makes familiar teaching quicker to pass. It survives a reload; "Reset all progress" is the only thing that clears it. It's separate from the Simulation Core's own state snapshot, and from the Session Log.
_Avoid_: Autosave, save file, save format, checkpoint

**Session Log**:
The versioned record of what a student did and answered during play, kept on the device, and shaped so it can later be sent to the instructor's sheet.
_Avoid_: Analytics, telemetry, save file
