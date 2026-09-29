# Level 5 — Chloroplast (working title)

Players see the title **Lock In** (`docs/story.md`). This file is the full spec, decided with the teacher in [Specify Level 5 — Chloroplast](../tickets.md#ticket-66).

It follows [`spec-template.md`](spec-template.md): the build contract comes first and the rationale is in the appendix. The rules for every level are in [`docs/levels.md`](../levels.md), the shared numbers and UI rules in [`shared-defaults.md`](shared-defaults.md), and terms follow `CONTEXT.md`. [`level-4.md`](level-4.md) is the most recent worked example.

**Units.** Every frame count and every per-frame quantity here is in **Baseline frames at 30 Hz**, the fork's rate, converted by the rule in [`level-3.md`](level-3.md) (*Units*): durations double in core ticks, speeds halve per tick, and per-frame charges and intervals convert the same way. Never mix the two units.

**Flagged for balance.** The class expects every student to finish this level (`docs/levels.md`). Any Tuning change carries the Pedagogy Rationale stated there, and needs a human playtest as well as Balance Runs. This spec ships the Baseline numbers and **pre-approves no change** (*Balance levers*, below).

## Summary

| | |
|---|---|
| Tier | Required up to the **Class Minimum**; expected to be completed. **LT2 is complete by the end of this level.** |
| Teaches | **LT2:** the chloroplast uses light *energy* to build glucose from carbon dioxide and water (accuracy rule 10), and gives off more peroxide as a result; chloroplasts divide; surplus glucose is stored as fat. **LT1:** defensins are proteins. **Organelle origin:** the endosymbiosis small print. **Experienced:** a bigger membrane dilutes the same defensins; wrapped viruses get past them. |
| Objectives | 10 (beats table) |
| Waves | Wave 1: 10 invaders. Wave 2: 15 invaders, the first 7 wrapped in vesicles. Sent one after the other. |
| Won | Wave 2 defeated |
| Lost | Membrane at 0 (lysis), nucleus at 0, or Starvation |
| Dead End | Possible through the declared predicate *No chloroplast left to divide*, only under the full classification below. Two candidates await validation in the build. |
| Level Rating | The shared rules, from 2 waves weighted 10 and 15 |
| In-level time | About **11 min** for a typical student (working range about 10–14 min): about 6 min of play and about 5 min of teaching. A **scheduling estimate**, reported, never a pass/fail criterion; checked by playtest. |

Level 5 is the class goal. The chloroplast arrives within the first half-minute, and acknowledging its introduction is the Class Minimum. The rest of the level puts it to work: more glucose, more peroxide to manage, fat to store, and defences for the flight.

## Shared infrastructure used

This section links to the shared systems; don't rebuild any of them for this level. The list comes from [`infrastructure-index.md`](infrastructure-index.md).

| Shared system | Where | Level 5 specifics |
|---|---|---|
| Game Shell state machine | `architecture.md`: Game Shell | Pause and the encyclopedia exist from the first frame. Level select reads the saved Class Minimum (extension below). |
| Objectives, and the minimum beat | `architecture.md`: Simulation Core; [shared defaults](shared-defaults.md#objectives-completion-and-the-linger) | 10 Objectives. The ones a linger could lose are **state predicates** (Beats). |
| Story Hold | Same; [shared defaults: Story Holds](shared-defaults.md#story-holds) | 12 holds, 2 of them between Objectives (beats table). |
| Game Events | `architecture.md`: Game Events | Engulfing, peroxide absorptions, division, toggles, purchases, auto-replacement attempts (succeeded or missed), defensin fusion and removal, defensin blocks, wave events, and **`class_minimum_reached`** (once per save). |
| Loss Routes, Starvation | [`levels.md`: shared end-of-level rules](../levels.md#the-shared-end-of-level-rules) | **Starvation is on**, with the shared values. All three Loss Routes can be live (*Win and loss*). No advice line is added. |
| Win-impossible predicates, Dead End | Same | One declared predicate and two candidates (*Win and loss*). |
| The Restart button | Same; [shared defaults: Restart](shared-defaults.md#restart) | The shared rule, replacing the Baseline's "no working chloroplast" dialog (S2). Familiarity never grants an action. |
| Idle watchdog | [shared defaults](shared-defaults.md#the-idle-watchdog) | Progress and highlight per Objective (beats table). |
| Spark preview and refusal | [shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) | Used unchanged. |
| Tuning | `architecture.md`: Content; [shared Tuning](shared-defaults.md#tuning) | This spec's Tuning section. No shared value is overridden. |
| Level Rating | [`levels.md`](../levels.md#the-level-rating) | Waves of 10 and 15. |
| Session Log | [Define the Session Log and save format](../tickets.md#ticket-15) | Also records the Class Minimum's tick, chloroplasts recycled, divisions of each organelle, mitochondrion switch-offs and their durations, auto-replacements missed, defensins made, fused and taken back, and defensin blocks. |
| The adapters | `architecture.md`: Renderer | No new adapter. |
| Selection and touch targets | [shared defaults](shared-defaults.md#touch-and-selection) | Chloroplasts join the selectable organelles. The cell is at its most crowded so far (10 lysosomes at the start): the playtest touch check covers it. |
| The world edge | [shared defaults](shared-defaults.md#the-world-edge) | Level 5 declares its own geometry and look (Starting state). |
| Left-behind gems | [shared defaults](shared-defaults.md#gems-not-drawn-from-a-pool) | No scripted gems in this level. |
| Persistence | `architecture.md`: Persistence | The Class Minimum is saved (extension below). |
| Balance Runs and the stuck-state search | [`levels.md`: Testing](../levels.md#testing) | Verification. |

**Starting state is declared, never carried over.** Level 5's content declares its full starting state, including the actions each organelle offers, and the core enforces availability (`architecture.md`: Content). First entry, Restart and replay from level select all start the same.

**Baseline simulation systems used here for the first time.** The catalogue (`docs/research/baseline-mechanics.md`, corrected in §K) fixes their rules, so they need no design ticket. They are shared systems, owned by the build and **never implemented privately inside Level 5**; the infrastructure index lists them:
- **the chloroplast** (production scaled by sunlight, peroxide, producer damage, Divide, recycling by lysosomes) (§1.3, §3.1, §6, §K)
- **sunlight**, a level-wide value (§6, §K)
- **auto-replacing peroxisomes**, the ER's toggle (§3.1, §K; D25 fixed)
- **defensins** (§2.5, §5.1, §K)
- **vesicle-wrapped viruses** (§5.1, §5.3, §K)

Levels 3 and 4's systems are used unchanged: small-unit recycling, membrane growth and shrinking, peroxide and peroxisomes, DNA repair enzymes, producer damage, the Golgi, lysosomes, organelle recycling by lysosomes (now also for chloroplasts), mitochondrion division and invaders.

**Extensions needed.** They block the Level 5 **build slices that use them**, not this spec (the map's *Sequencing* note). If one changes an Objective, a timing or an available player action here, that part of this spec is revisited.
- [Persistence extension: save the Class Minimum](../tickets.md#ticket-101). The save gains a never-lost Class Minimum, written the moment it is reached; level select reads it for the Recap's copy button.
- Used unchanged: [the camera](../tickets.md#ticket-82), [Action Panel purchases](../tickets.md#ticket-83) (its honest refusal carries the FA fix line and the missed-replacement note, below), [wave presentation](../tickets.md#ticket-84) (two sequential waves; no concurrent waves), [group selection and group actions](../tickets.md#ticket-87), and [organelle Move and Toggle](../tickets.md#ticket-88) (chloroplast Move and Toggle use it).

**Open dependencies:**
- [Verify the chloroplast's production cycle](../tickets.md#ticket-100). The chloroplast's cycle length is **pending verification**. It is never a silently fixed constant: the build slice that implements chloroplast production lists this ticket as a blocker.
- [Verify the mitochondrion's production cycle and check the Starvation warning against it](../tickets.md#ticket-85), as in Levels 2–4.

## Starting state

| | |
|---|---|
| Resources | **Starting resources** (Tuning) |
| Units | **Starting units** (Tuning): 1 working mitochondrion, **30 ribosomes**, 10 lysosomes (ready), 3 peroxisomes, no slicers, no chloroplast, no pending production. The nucleus, the ER and the Golgi are present. The **chloroplast** arrives when the Start hold closes. **The 30 ribosomes are intentional (Baseline):** the ribosome cap of 25 refuses *further purchases* "at maximum" all level; it never rejects the starting state or removes ribosomes. |
| Actions | Declared at load (Baseline menus): **Nucleus:** Buy Ribosomes (refused "at maximum" all level), Buy Slicers, Buy DNA Repair. **ER:** Make Membrane, Take Membrane, Buy Peroxisome. **Golgi:** Buy Lysosome. **Mitochondrion:** Move, Toggle, Recycle, Divide. **Lysosome, peroxisome:** Move, Recycle. **Slicer, DNA repair enzyme** (once bought): Recycle. **Chloroplast** (from its arrival): Move, Toggle, Recycle. The rest are enabled when their Objective is armed (Beats): chloroplast **Divide**, the ER's **Auto-replace** toggle, **Make Defensin**, **Take Defensin**. |
| World | The rocket's dish, in flight: **declared in Level 5's own content** (the **dish radius**, world-fixed bands and a drawn rim; the look is the art pipeline's), with the cell at the centre. Pulls are clamped by the **edge clamp**. |
| Membrane | 15 nodes and 100 HP max (**membrane**, Tuning), so the lens is about 1,191 px. No defensins. |
| Camera | The **starting zoom**, with the zoom control shown |
| HUD | The ATP, NT, AA, FA and glucose meters, the membrane-health gauge, the **sunlight meter** (at 100%), the Objective pane, Pause and the encyclopedia button, from the first frame. The defensin shield appears on the membrane-health gauge with the first fused defensin. |
| Pools | The glucose pool (fixed, **no regrowth**) and the AA pool (**pools**, Tuning) are **on from the first frame**. No NT or FA pool. |
| Sunlight | 100% all level (**sunlight**, Tuning). Only the Sunlight hold's illustrative demonstration shows it change. |
| Producers | The mitochondrion burns glucose from the start (**metabolism**, Tuning). Glycolysis doesn't run while any mitochondrion exists. **Fat processing is on.** **Peroxide production is on from the first frame** (no burst), with none on a producer's first cycle. |
| Movement | Free from the first frame |

## Beats

Every row with a hold is a **Story Hold**. The scripts ticket ([Script the flight: Levels 5–6 and the Arrival cutscene](../tickets.md#ticket-74)) writes the lines; this table fixes what each beat covers. "Between" marks a hold that opens between two Objectives rather than introducing one.

| Story Hold | Objective | Completes when (a state predicate) | Switches on | Movement | HUD reveals | Watchdog progress and highlight |
|---|---|---|---|---|---|---|
| **Start.** Mid-flight, the dish's glucose is running low. R.Y.E. releases the chloroplast he packed "just in case". The idea: give this cell a chloroplast. No line says chloroplasts are found only in plant cells (algae and other protists have them). | 1. **Get the chloroplast** | Chloroplasts **≥ 1** (a chloroplast counts from its creation at engulfment, Baseline) | **When this hold closes:** the chloroplast is placed (Scripted spawns), and the **arrival zoom** runs | Free | — | A pull toward it, or its distance to the membrane shrinking; the chloroplast (off-screen arrow while outside the view) |
| *Between:* **Chloroplast.** It's in. The chloroplast uses light **energy** to build glucose from carbon dioxide and water (rule 10). **Shown:** glucose leaving the chloroplast. The chloroplast's encyclopedia entry unlocks, with the endosymbiosis small print (*Science accuracy*, below). **Acknowledging this hold is the Class Minimum** (below). | — | — | — | — | — | — |
| **Sunlight and peroxide.** Opens after the **hold gap** (play running). **Illustrative demonstration** (not the student's own chloroplast, and nothing in the simulation changes): the lamp dims, the sunlight meter falls, and the chloroplast's glucose output slows and then stops; the lamp comes back and production resumes. More light, more glucose; no light, none. Then the warning: chloroplasts give off **more** hydrogen peroxide than mitochondria. Keep about **2 peroxisomes per chloroplast**. | 2. **Break down 5 peroxide** | **5 absorptions** by any peroxisome, of peroxide from any producer, counted while the Objective is armed | — | Free | — | Each absorption; the nearest peroxisome |
| **Divide.** It worked. Ursa: the cell is **adapting**; Aqua corrects it on the spot, with the correction agreed in `docs/story.md` (the one allowed use of the word). The cell makes its own sugar now. Chloroplasts can divide, like mitochondria. | 3. **Divide the chloroplast** | Chloroplasts **≥ 2** (a daughter counts when it appears, Baseline) | Divide on chloroplasts, **when Objective 3 is armed** | Free | — | A Divide accepted; the chloroplast's Divide action (any chloroplast's) |
| **More peroxide.** More sugar means more peroxide. Keep mitochondria and chloroplasts apart so peroxisomes can catch it in between (peroxisome and producer Move). | 4. **Break down 10 more peroxide** | **10 absorptions**, counted while armed, as Objective 2 | — | Free | — | Each absorption; the nearest peroxisome |
| **Auto-replace.** The ER can **attempt** to replace each worn-out peroxisome. **Demonstration:** a peroxisome wears out and the ER orders a replacement at full price, which buds from the ER; then an attempt that can't be paid is **missed**, and the missed count appears at the ER. It never adds peroxisomes: build them first. | 5. **Switch on auto-replace** | The ER's auto-replace is **on** | The ER's Auto-replace toggle, **when Objective 5 is armed** | Free | — | The toggle switched on; the ER, then its Auto-replace action |
| **Fat.** The cell stores surplus glucose as fat: 4 surplus glucose become 1 fatty acid (metabolism; fat is the longer-term store, rules 3 and 4). Switching a mitochondrion **off** stores glucose faster, but it makes no ATP while off. | 6. **Make 10 fatty acids** | **FA ≥ 10.** A student already holding 10 completes after the introduction and the minimum beat. | The **ATP tip** is armed (Rules) | Free | — | Each FA gained; the FA meter |
| **Defensins.** R.Y.E.: stowaway viruses in the cargo. **Defensins** are proteins the Golgi packages and ships to the membrane (LT1: built from amino acids). Supplies appear as the cell **moves** (the accurate form of the Baseline's tip). | 7. **Make a defensin** | A Make Defensin order is **accepted** | Make Defensin, **when Objective 7 is armed** | Free | — | An order accepted; the Golgi, then its Make Defensin action |
| **Defensin strength.** More defensins give a higher chance to block a virus; the chance is **capped**. **Illustrative demonstration:** the same defensins spread over a bigger membrane, and the shield's chance drops. | 8. **Make 5 defensins** | Defensins **fused in the membrane ≥ 5** (D11 fixed). A student already at 5 completes after the introduction and the minimum beat. | — | Free | The shield on the membrane-health gauge, labelled as the **chance to block an unwrapped virus** (if not already shown) | Each order or fusion; Make Defensin, **cleared** once fused plus ordered reach 5 |
| **Shield.** The shield shows the chance to block an unwrapped incoming virus. R.Y.E. is unimpressed and sends the first wave. | 9. **Survive the viruses** (armed when this hold closes, **at the send**) | Wave 1 defeated **and** its defeated card acknowledged | **Wave 1** is sent when this hold closes | Free | The wave display (wave ticket) | Any drop in the wave's live plus dormant count; Buy Lysosome, **suppressed** when the cap blocks another purchase |
| **Wrapped viruses.** After wave 1's card is acknowledged. R.Y.E.: try this. Some viruses arrive **wrapped in a vesicle**: they skip the defensin roll and enter without an entry wound, but still damage the membrane on the way out. Defensins can be taken back for their building blocks. | 10. **Survive the wrapped viruses** (armed at the send) | Wave 2 defeated **and** its defeated card acknowledged | **Wave 2** is sent when this hold closes; **Take Defensin**, when Objective 10 is armed | Free | — | As Objective 9 |
| **Win.** After the last card. The flight goes on (the scripts ticket). Then the results screen. | — | — | — | Locked | — | — |

### When effects happen

As Levels 3 and 4 ([`level-4.md`](level-4.md), *When effects happen*): completion effects on completion; the next hold opens when the completion linger ends; its *Switches on* effects apply when it closes, and then the next Objective is armed, which is when its action is enabled. The Baseline's per-Objective delays (45 frames after the engulf, 200 frames after the chloroplast's introduction, and the rest) are replaced by the shared completion linger and the **hold gap**; none of them times a demonstration.

**The two holds after Objective 1.** Objective 1 completes; after the completion linger, the **Chloroplast** hold opens. When it is acknowledged, play runs for the **hold gap**, then the **Sunlight and peroxide** hold opens and introduces Objective 2. The Baseline's second dialog (the sun lamp and the peroxide warning) keeps its own beat; it is not merged into the first or dropped.

### The Class Minimum

- **Reached when the Chloroplast hold is acknowledged**, not at engulfment: the student has then seen the introduction (`CONTEXT.md`).
- The core emits **`class_minimum_reached`** on that tick, **only if this save hasn't reached it before**. The persistence adapter writes it **at once**, before anything else can end the attempt, so a refresh, a loss or a Restart afterwards can't lose it ([the persistence extension](../tickets.md#ticket-101)).
- **Every attempt can earn it until it is saved.** A student who restarts (or loses) before acknowledging the Chloroplast hold earns it at the same point on a later attempt. Once it is saved, a Restart or replay emits nothing. Fast-forwarding the Chloroplast hold still acknowledges it (the shared fast-forward contract).
- **No line plays in the level** (`docs/story.md`, rule 9). Level select shows the Recap's copy button from then on.

### Notes on the beats

- **Actions are enabled when their Objective is armed** (chloroplast Divide with 3, Auto-replace with 5, Make Defensin with 7, Take Defensin with 10), and the core refuses each before then, including during the previous Objective's completion linger. Each stays available for the rest of the level. Everything earlier levels taught is available from the first frame (Starting state), including the chloroplast's Recycle from its arrival. **Familiarity never grants an action.**
- **Recycling the only chloroplast (Baseline kept).** Recycle is offered on the chloroplast from its arrival (organelle recycling was taught in Level 4). Recycling the only one before Objective 3 is satisfied makes the level unwinnable: the declared predicate *No chloroplast left to divide* fires, with a Restart offer under Objective 3. **No confirmation and no restriction:** playtests record how often it happens. The Baseline's "no working chloroplast" dialog (S2) is replaced by the shared Restart rule.
- **Damage never removes a Divide parent.** Divide has no health check (Baseline), so a chloroplast damaged by peroxide still divides into a full-health daughter.
- **The peroxide Objectives count events while armed.** Absorptions are automatic and continuous, so absorptions during a completion linger aren't carried over (the shared rule), and no student work is lost. Objectives 2 and 4 each count from their own arming. They usually complete without the student doing anything; the teaching is the peroxide on screen.
- **Present and ready, fused and ordered.** Objective 8 counts defensins **fused in the membrane**, the only ones that protect. The progress display shows fused and ordered separately. Lysosomes are present versus ready as in Level 4.
- **Objectives 9 and 10 are armed at the send** (Baseline here: the fork sends each wave and arms its Objective together). Each completes only after its wave's card, and so its reward, has been acknowledged.
- **Starvation advice** is the shared table; no line is added. The level's own **ATP tip** (Rules) is a separate nudge.
- **The first-time Starvation card** may open at any point where the warning first shows (shared rule).

### Science accuracy for the scripts

- **Rule 10.** The chloroplast uses light *energy* to build glucose from carbon dioxide and water. Never "makes glucose from light" or "from sunlight".
- **The adapting line** is Ursa's, paired with Aqua's agreed correction in `docs/story.md`. No other line says evolve, evolution, adapt or natural selection.
- **Not "only in plant cells".** Plants and algae (and other protists) have chloroplasts.
- **The endosymbiosis small print** (the chloroplast's encyclopedia entry, rule 6), with no "evolve" wording:
  - chloroplasts come from bacteria that once lived freely and made their own food, and were taken in by a cell long ago;
  - the evidence: they have their own DNA and a double membrane, and they divide by themselves;
  - real cells get chloroplasts by **inheriting them when they divide**, not by absorbing them; the game's absorbing is a shortcut;
  - the nearest real case: some sea slugs keep working chloroplasts taken from the algae they eat.
- **Defensins** are proteins (LT1). The shield's number is a game number (rule 5): it is labelled as the chance to block an unwrapped virus, never as biology.
- **Fat** is made *from* surplus glucose by metabolism; it is the longer-term store (rules 3 and 4).

## Scripted spawns and rules

### Scripted spawns

| Spawn | Placement | Visibility | Left behind |
|---|---|---|---|
| **The chloroplast** (Baseline) | When the Start hold closes: one, inside a vesicle wrapper, at a seeded random angle at the **float-in distance** from the centrosome, homing on the centrosome at the **float-in speeds** | Visible | Never left behind: it homes until the membrane touches it, and is then engulfed, becoming a live chloroplast at full health, nudged inside the cell. It isn't a gem and draws on no pool. |

### Rules

| Rule | Trigger (armed between) | Effect | Cooldown and limits |
|---|---|---|---|
| **Pools** (Baseline) | From the first frame to the end: a spawn roll per pool every **roll distance** of travel | One gem just outside the lens, at a random angle (shared pool rules) | Glucose: a **fixed pool with no regrowth**; a missed gem returns +1 to it. AA: regrowth every **regrowth interval**, no ceiling. **D15:** a roll with no room makes no gem and costs the pool nothing. |
| **Purchases** (Baseline, D18) | Each action from its Objective's arming, or from the first frame (Starting state) | The shared purchase rules, with honest refusals (Action Panel contract) | Caps count units **ordered** as well as present. Ribosomes are refused "at maximum" (30 held, cap 25). |
| **Production** (Baseline, §3.2) | Any order | As Level 4, plus **defensins**: RNA → ribosome at the ER → a small vesicle → the Golgi's **Golgi step** → a defensin vesicle travels to the membrane and fuses, **+1 defensin** | NT in flight is the only throughput limit the level adds. NT is a closed budget: the starting 10, recycle refunds and wave rewards (no NT pool). |
| **Chloroplast production** (Baseline, §1.3, §K) | A chloroplast switched on, not damaged, not dividing, not inside a digestion vesicle, sunlight above 0 | 1 G per **chloroplast cycle**. It costs nothing to run. | The cycle length is **pending verification** ([the chloroplast timing ticket](../tickets.md#ticket-100)). Toggle is free. |
| **Chloroplast peroxide** (Baseline, §6, §K) | Each chloroplast cycle after its first | **1 peroxide per cycle**, emitted at the cycle's start, at the **peroxide speed**, targeted by the **targeting weights** | Never expires; the same targeting as a mitochondrion's |
| **Divide a chloroplast** (Baseline, §1.4, §K) | From Objective 3's arming, on **any** chloroplast, damaged or not | The **chloroplast divide cost**, checked and paid at the command. After the **divide time**, a **full-health** daughter appears beside the parent and counts at once. The parent keeps its own health. The daughter makes no peroxide on its first cycle. | **No health check.** The **chloroplast cap** counts all chloroplasts. Refused while that chloroplast is already dividing. |
| **Recycle a chloroplast** (Baseline; §1.4, §J, §K) | From its arrival | Level 4's organelle recycling by lysosomes, unchanged, with the **chloroplast recycle charge and refund** and the **lysosomes needed (a chloroplast)**. Cancel Recycle as Level 4, acid damage kept. | One vesicle per organelle; waiting shown as progress |
| **Mitochondrion Divide and Recycle** (Baseline) | From the first frame | As Level 4 | As Level 4 |
| **Auto-replace peroxisomes** (Baseline, §3.1, §K; **D25 fixed**) | The ER's toggle on (from Objective 5's arming; off in the starting state; free to flip) | When a peroxisome **wears out** (health 0, not a player recycle), the ER **attempts** one purchase of a peroxisome at **full price**, through the normal pipeline. **The order (Baseline, verified in the fork, §K):** when the worn-out peroxisome's recycle animation ends, its **refund** (50 AA and 2 FA; no ATP is charged for a wear-out) is credited **first**; **then** the replacement is attempted; the dead unit leaves the count last. **D25 fixed:** the dead unit leaves the cap count **before** the attempt instead of after. Nothing else moves: the attempt's affordability check still sees the refund already credited, exactly as in the Baseline. | **No retry** (Baseline). A **failed attempt** is reported honestly in the Action Panel contract's refusal style, anchored at the ER ("Couldn't replace a worn-out peroxisome: not enough ATP", working text), and the ER shows a **missed-replacement count** until the student buys a peroxisome, which clears it. The toggle stays on. |
| **Surplus glucose to fat** (Baseline) | As Level 3 | As Level 3 | On all level |
| **Mitochondrion** (Baseline) | Switched on, not damaged, with fuel | As Level 4, including the fat backup | Its cycle length is [the timing ticket](../tickets.md#ticket-85). Glycolysis runs **only when no mitochondrion exists**. |
| **The ATP tip** (Deviation, presentation) | From Objective 6's arming to the end of the level | While **ATP ≤ the ATP tip threshold and a working mitochondrion is switched off**: a short non-blocking nudge line ("Low on ATP. Switch your mitochondrion back on.", working text) with the mitochondrion's Toggle as its highlight. Not a Story Hold; nothing pauses. | Shown once per switch-off: it **re-arms** when every working mitochondrion is switched on again. Cleared at once when the condition stops being true. |
| **The FA fix line** (Baseline tip kept, D26) | Any purchase refused for fatty acids, from the first frame | The Action Panel contract's honest refusal names FA and carries the fix line "Recycle something, or let surplus glucose become fat" (working text; the contract owns the wording) | Every FA refusal; true every time, so nothing disarms it |
| **Defensins** (Baseline, §2.5, §K) | Make Defensin from Objective 7's arming | **Defensin cost**, 1 per order, **defensin cap** counting ordered units. Strength = fused defensins ÷ membrane nodes, capped at the **defensin cap chance**, recomputed on every membrane change. | Membrane growth dilutes it; shrinking concentrates it |
| **Defensin roll** (Baseline, §5.1) | An **unwrapped** virus arriving from outside touches the membrane | One roll against the strength: on success the virus dies with a "Blocked!" marker; otherwise it proceeds normally (an invader pierces for 1 membrane damage) | One roll per arrival. Wrapped viruses skip it. |
| **Wrapped viruses** (Baseline, §5.1, §K) | A wrapped virus touches the membrane | Its vesicle is shed; **no defensin roll** and **no entry wound**; it proceeds inside as an invader | Its virions inherit the wrap and still do 1 membrane damage each **on the way out** |
| **Take Defensin** (Baseline, §2.5, §K) | From Objective 10's arming | Removes 1 fused defensin **at once**, charging the **defensin take charge** and returning its **defensin take refund** | Refused honestly with no defensins or short ATP |
| **Lysosomes and invaders; invaders; slicers** (Baseline) | As Level 4 | As Level 4 | As Level 4 |
| **Waves** (Baseline, §5) | Wave 1 when the Shield hold closes; wave 2 when the Wrapped viruses hold closes | The **waves** (Tuning), presented by the wave contract. On a send with no wave active, the camera zooms out (**wave zoom-out**). | Each wave's defeat, rating, card and reward are its own |
| **Membrane repair** (Baseline, §2.3) | Membrane below max health | As Level 3 | Skips silently while ATP is below the repair cost |
| **Peroxide** (Baseline, §6, §I, §J) | From the first frame, from every working producer switched on | As Level 4, with **chloroplasts added to the targeting weights**; **D21's band kept** | Never expires. Never targets the membrane, the ER, the Golgi or ribosomes. |
| **Producer damage** (Baseline, §3.4) | A mitochondrion's or chloroplast's health below the **stop threshold** | It stops producing for good. Remedies: Recycle and Divide. | S1 and S2 are replaced by the shared Restart rule |
| **Peroxisomes; DNA repair enzymes** (Baseline; reuse Deviation) | From the first frame | As Level 3 | As Level 3 |
| **Spark preview and refusal** (shared) | Any pull | [Shared defaults](shared-defaults.md#the-pseudopod-spark-preview-and-refusal) | — |
| **Idle watchdog** (shared) | Per the beats table | A non-blocking nudge and its highlight | Shared gaps |

**No tutorial help.** No resource arrives beyond the pools, the chloroplasts' production, the refunds and the wave rewards.

## Tuning

Every number is Tuning. Frame counts and per-frame quantities are Baseline frames at 30 Hz. The source is the fork's slot 4 and its source code unless marked. The rows marked *provisional* or *pending verification* hold until the build, a verification ticket or playtests confirm them.

| Name | Value | Source | Checked by |
|---|---|---|---|
| Starting resources | ATP 1000, NT 10, AA 550, FA 30, G 50 | Baseline (the walkthrough's FA 10 and G 0 disagree; the fork rules on numbers, §K) | Unit test |
| Starting units | 1 working mitochondrion (100 of 100 HP); **30 ribosomes** (above the cap of 25; kept, never trimmed); 10 lysosomes, ready; 3 peroxisomes; no slicers; no chloroplast; no pending production | Baseline | Unit test |
| Membrane | 15 nodes; 100 HP max; max 25 nodes; +10 max HP per node | Code default (D10); Baseline §2.2–2.3 | Unit test |
| Dish radius; edge clamp | As Level 4 (5,000 px; centrosome ≤ dish radius − cell radius) | Baseline; provisional | Boundary invariant |
| Starting zoom; wave zoom-out | As Level 4 | Baseline | Camera ticket |
| Arrival zoom | Zoom scale 0.025 (screen scale 0.15) over 30 frames when the Start hold closes | Baseline | Camera ticket |
| Pools | **G 150, no regrowth**; AA 250 at 0.5; both on from the first frame. No NT or FA pool. | Baseline (the fork declares NT 50 and FA 50 pools that never activate; omitted) | Resource-acquisition runs |
| Regrowth interval; roll distance | 16 frames; 72 px | Baseline effective timing (D16) | Unit test |
| Gem values | As Level 2 | Baseline | Unit test |
| Sunlight | 1 (100%) all level | Baseline | Unit test |
| Float-in distance; float-in speeds | 1.05 × lens; as Level 4's float-in (§8.5). About 26–28 s to reach a waiting cell. | Baseline | Unit test |
| Hold gap | 1.5 s, play running, between the Chloroplast hold and the Sunlight and peroxide hold. Its own entry, sharing the completion linger's starting value. | Provisional (replaces the Baseline's 200 frames) | Playtest |
| **Chloroplast cycle** | **Pending verification.** Working estimate: a counter that must exceed int(30 ÷ sunlight) frames, then a process animation of about 52 frames with the counter frozen, 1 G credited at its end: **about 83 frames per G at 100%**. Never a fixed constant until [the chloroplast timing ticket](../tickets.md#ticket-100) closes. | Baseline reading (medium confidence) | The timing ticket; then a unit test |
| Chloroplast | 100 HP; costs nothing to run; the counter starts at 0; stops below the stop threshold; peroxide targeting weight 10; move cost 50 ATP per 500 px; Toggle free | Baseline | Unit test |
| Chloroplast peroxide | 1 per cycle after the first, emitted at the cycle's start | Baseline (the fork's 25% chance field is dead code; not modelled) | Unit test |
| Chloroplast divide cost; divide time | 200 ATP, 5 NT, 100 AA, 25 FA, 10 G, at the command; 40 frames, then a full-health daughter 75 px away | Baseline §1.4 (the walkthrough agrees) | Unit test |
| Chloroplast cap | 10, counting all chloroplasts, damaged ones included | Baseline | Unit test |
| Chloroplast recycle charge; refund | 100 ATP charged at the command, never returned; 5 NT, 100 AA, 25 FA, 10 G credited when the organelle recycle animation after its death ends | Baseline §1.4 | Unit test |
| Lysosomes needed (a chloroplast) | **8, provisional**, never derived from our art | Baseline reading; a checklist item in [the validation task](../tickets.md#ticket-99) | Unit test; the validation task |
| Peroxide targeting weights | Mitochondrion 10, **chloroplast 10**, peroxisome 1, slicer 0.5, lysosome 2, each × count; the nucleus gets ⅓ of that sum; the total × 1.05. **D21's band kept.** Shares of **initial target selection** only; peroxisomes intercept in flight, and a targeted peroxisome takes the peroxide damage rather than the absorb wear. | Baseline §6, §J | Statistical unit test |
| Peroxide rate (mitochondrion); peroxide damage; peroxide speed | As Level 4 | Baseline | Unit test |
| Peroxide Objectives | 5 (Objective 2); 10 (Objective 4); absorptions counted while armed | Baseline (15 one-step Objectives collapsed into 2 counted ones) | Unit test |
| Peroxisome cost; cap; health; absorb wear; refund; speed | As Level 3 (50 ATP, 50 AA, 2 FA plus 1 NT in flight; cap 10 counting ordered; 20 HP; 1 per absorption; 50 AA and 2 FA on wear-out; 4 px per frame) | Baseline | Unit test |
| Auto-replace | Off at load; one full-price attempt per wear-out, after the wear-out refund is credited; none for a player recycle; no retry; D25 fixed | Baseline, §K (the ordering read from the fork) | Unit test |
| FA target | 10 (Objective 6) | Baseline | Unit test |
| ATP tip threshold | 500 | Baseline | Unit test |
| Defensin cost; cap | 100 ATP, 50 AA, 1 FA, plus 1 NT tied up in RNA; 1 per order; cap 50, counting ordered units | Baseline §1.4 | Unit test |
| Defensin strength; defensin cap chance | Fused defensins ÷ membrane nodes; capped at 0.5 (at 15 nodes: 5 give 33%, 8 reach the cap) | Baseline §2.5 | Unit test |
| Defensin target | 5 fused (Objective 8) | Baseline | Unit test |
| Defensin take charge; refund | 50 ATP; 50 AA and 1 FA, at once | Baseline §2.5, §K | Unit test |
| Waves | Wave 1: 10 invaders, none wrapped. Wave 2: 15 invaders, **the first 7 wrapped** (⌊15 × 0.5⌋); each re-attack wraps ⌊pool × 0.5⌋ again. Spawn rings, alert duration and dormancy as Level 2. | Baseline §5, §K | Balance Runs |
| Wave rewards | Wave 1: 1 NT and 25 AA. Wave 2: 2 NT, 38 AA and 2 FA. Each credited once, when its card is acknowledged. | Baseline (formula §J; rounded) | Unit test |
| Lysosomes; invaders; slicers; DNA repair; mitochondrion (health, stop threshold, Divide, recycle, move); membrane growth, shrink and repair; nucleus | As Level 4 | Baseline | Unit test |
| Metabolism | As Level 4, including the fat backup. **The cycle length is open** ([the timing ticket](../tickets.md#ticket-85)). | Baseline | Unit test, after the ticket |
| Starvation, lingers, nudges, touch | The shared values; **no overrides** | [Shared Tuning](shared-defaults.md#tuning) | — |

### Balance levers

The Baseline numbers ship. **No Tuning change is pre-approved.** If playtests show a wall:
1. **Presentation first:** for example, the mitochondrion's on/off state made obvious at a glance, the peroxisome-per-chloroplast guide shown rather than told, the missed-replacement count made clearer.
2. **Then whichever mechanical lever the observed problem supports** (starting stock, wave composition, or another), with no order fixed in advance.
3. **Organelle Repair** (a dormant fork action) stays the last, drastic resort, only with a strong pedagogy reason.

Every change carries the Pedagogy Rationale in `docs/levels.md` (*the class expects every student to complete this level within one period, so a wall here costs students the chance to use the chloroplast they just earned*) and needs a human playtest as well as Balance Runs. D21 stays as played; its controlled comparison is optional evidence under [Level 4's validation task](../tickets.md#ticket-94). A time estimate running tight against the class budget is **not** a reason to rebalance.

### Economy hypotheses (reported, never asserted)

These come from the fork reading and are **hypotheses for the Balance Runs to report**, never balance guarantees. They depend on the unverified production cycles, and they treat target-selection shares as if they were hit shares, which they aren't (peroxisomes intercept in flight, and a targeted peroxisome takes different damage from interception wear).
- One chloroplast's glucose may roughly match one mitochondrion's burn at 100% sunlight; two may give a surplus that becomes fat.
- With two chloroplasts and the mitochondrion on, peroxisomes may wear out every few tens of seconds; with auto-replace on, the net cost is mostly ATP (the wear-out refund returns the AA and FA, D23).
- The fixed glucose pool (about 420 G on average) is a buffer; the chloroplasts carry the long-run economy **while something can burn their glucose**.
- The likeliest walls: wave 1 against lysosomes lost to peroxide (D21's band loads them); the ATP spend (Divide 200, defensins 500); a mitochondrion left switched off after the fat beat.

## Win and loss

- **Win:** wave 2 defeated and its card acknowledged, while Objective 10 is armed, under the shared Objective rules.
- **Loss Routes.** Each answers possible, impossible or **unknown**, as the shared rule allows, **reclassified continuously**. Every check reads Objectives' **recorded satisfaction state** and treats pending sources (an order, a daughter, a cancellable digestion) as still able to arrive.
  - **Lysis:** possible while any wave has live virions, viral RNA or a dormant pool, or while the chain of Objectives that sends one can still complete. Before wave 1, nothing damages the membrane.
  - **Nucleus at 0:** **possible** while a working mitochondrion **or chloroplast** is switched on. **Unknown** while only peroxide already in flight remains, or while a working producer exists but is switched off, without fuel (a mitochondrion), or inside a digestion vesicle that can still be cancelled with its health at or above the stop threshold. **Impossible** when no producer can produce, none can come, and no peroxide exists.
  - **Starvation:** the shared rule. Possible whenever ATP is below the Line, or an automatic drain could take it there (membrane repair after wave damage). Spending by choice doesn't count.
- **The exhaustion fact** (a diagnostic, never a predicate on its own) is narrowed as in Levels 3 and 4: **no remaining route can increase ATP.** In Level 5 the routes are:
  - a working mitochondrion, switched on or off, with fuel: G ≥ 1, FA ≥ 1, a cycle under way, FA still to come, glucose still to come from a working chloroplast, an affordable pull that could reach a gem, or a gem within motion reach;
  - **glycolysis** when no mitochondrion exists, with glucose held, reachable, or still to come from a working chloroplast;
  - a working chloroplast is a route **only through one of those two**: if the only mitochondrion is damaged but still exists, it can't burn the glucose and it blocks glycolysis, so recovery then needs an affordable **mitochondrion** Divide, recycling the damaged mitochondrion (so glycolysis can start), or another actual route;
  - **a mitochondrion Divide**, when affordable, or a mitochondrion daughter already on its way: the full-health daughter burns glucose. **A chloroplast Divide is not an ATP route by itself:** it adds glucose, which still needs one of the burners above;
  - **Cancel Recycle** on a producer still at or above the stop threshold.

  An **empty glucose pool proves nothing on its own**. The fact is logged for the loss hint ([hint rules](../tickets.md#ticket-80)) and for Balance Run reports.

### Declared predicate: *No chloroplast left to divide*

True when **all** of these hold (Level 4's safeguards, carried forward):
1. **A division is still needed:** Objective 3's predicate has not been satisfied. This reads the Objective's **recorded satisfaction state**, not "not yet completed", so a Divide that has already met it, with only the minimum beat left, never makes the predicate true;
2. **no chloroplast exists:** none in the cell, **none inside a digestion vesicle that hasn't died yet** (Cancel Recycle can still save it: damaged, perhaps, but a possible Divide parent), and **no daughter pending** from an accepted Divide;
3. **no scripted arrival is pending.** Level 5's one arrival, the chloroplast, is guaranteed to have been engulfed: Objective 1 needs it, and a chloroplast can only be recycled once it exists.

- **Why it is irreversible:** Objective 3 needs chloroplasts ≥ 2; Divide is Level 5's only source of chloroplasts after the scripted arrival, and Divide needs a chloroplast. The waves are sent only through Objectives 3 → 9, so the win is out of reach.
- **Not true for:** a chloroplast being digested but not yet dead; a Divide accepted and its daughter pending; Objective 3 already satisfied and waiting for its minimum beat; damaged chloroplasts (still Divide parents); zero chloroplasts **after** Objective 3 is satisfied (the level stays winnable on the mitochondrion, the pool and banked resources).
- **Reason line** (working text): "No chloroplast is left to divide."
- **Classification**, the shared rule's, reassessed continuously. A **Dead End** needs **every** one of these:
  - **no wave can still be sent** (true: the sending chain is blocked at Objective 3), so lysis is impossible;
  - **no automatic drain can lead to Starvation** (true before any wave: membrane repair runs only after wave damage), and ATP is **at or above the Line**;
  - **the nucleus route is impossible:** no producer can produce (a switched-off working mitochondrion still can, since Toggle is free; so every mitochondrion must be damaged or gone, with none inside a cancellable digestion vesicle), none can come (no Divide under way), and no peroxide is in flight.

  Otherwise it is a **Restart offer** under Objective 3. In practice a working mitochondrion keeps the nucleus route possible, so a Restart offer is the usual outcome.
- **Reachable example:** during Objective 3, the student recycles the only chloroplast and lets the digestion finish. The predicate fires on the tick it dies, as a Restart offer (the working mitochondrion still makes peroxide). The build demonstrates this example.

### Candidates, to validate in the build

Recorded honestly as **candidates**, not declared. [Validate Level 5's win-impossible predicates in the build](../tickets.md#ticket-99) either finds a reachable example that shows the shortfall and rules out recovery (then declares it, with a regression test) or drops it.

| Candidate | The claim | What must be ruled out | Reason line (working) |
|---|---|---|---|
| **W1 Can't afford the rest** | Before wave 1 is sent: no route in the exhaustion fact can increase ATP, and what is still needed can't be paid from what is held: the chloroplast Divide's ATP and materials while Objective 3 needs it; defensins beyond those fused and ordered, up to 5, while Objectives 7–8 need them; FA to 10 while Objective 6 needs it. At per-unit costs (D18). | Every route in the exhaustion fact, above all an affordable mitochondrion Divide (a chloroplast Divide only adds glucose), recycling a damaged mitochondrion to start glycolysis, Cancel Recycle, and glucose still to come from a working chloroplast that something can burn. Materials that refunds, the AA pool or surplus glucose can still supply. | "Not enough ATP left to finish getting ready, and no way to make more." |
| **W2 No defence left** | A wave has live or dormant viruses or viral RNA, **and** no functioning defence exists or can come: **no live lysosome** (one eating a virus is still live), **no slicer present**, **no fused defensin**, none of these ordered, growing or recoverable, **and** no ATP route to buy one at the real purchase rules | **Existing** lysosomes, slicers and fused defensins as well as orders, growing units and recoverable ones; any ATP route. Always a Restart offer (the wave keeps damaging the membrane), never a Dead End. | "No defences left, and no ATP to make more." |

- **Dead End status:** possible through *No chloroplast left to divide*, only under the full classification above. The candidates may add more; until the validation task settles them, none is claimed.
- **Results screen:** "Level complete!", the Level Rating from the two Wave Ratings, and the play time ([shared defaults: Clocks](shared-defaults.md#clocks-and-the-results-screen)). No best time.

## Verification

### Time targets

| Measure | Clock | Population | Target | Checked by |
|---|---|---|---|---|
| **In-level time** | From the first Story Hold to "Level complete!", in real time | A typical student (±1 SD) | About **11 min** (working range about 10–14): about 6 min of play and about 5 min of teaching. A **scheduling estimate**, reported, never a pass/fail criterion. | Human playtests |
| Class Minimum | Real time from the level's first Story Hold | A typical student | About 1½–2 min into the level: about **38–39 min** after starting Level 1, against the ≤ 45 budget (`docs/levels.md`) | Human playtests; the Session Log |
| Level complete | Classroom total from starting Level 1 | A typical student, one Restart | About **49–51 min**, against the ≤ 55 budget. Tight, and reported; **not a reason to rebalance**. | Human playtests |
| Teaching budget | Story Holds | A typical student | About 5 min across 12 holds | Human playtests; the scripts ticket writes to it |
| Stuck points | Where students stall, and whether they recover: **a mitochondrion left off at low ATP, the last chloroplast recycled, missed auto-replacements, Divide refused for FA** | All playtesters | Recorded, not targeted | Human playtests; the Session Log |
| **Bot regression band** | Play time | The competent autopilot | 200–420 s, provisional and **reporting-only** until calibrated | Balance Runs |

- The walkthrough (9:47 in the level, 4:33 of play, a B) is a practised player's.
- The cumulative figures are arithmetic on unvalidated estimates for Levels 1–4 (about 31 min in-level) plus cutscenes and one Restart.
- **If playtests run long, investigate avoidable delay first:** confusing controls, a hard-to-find target, or repeated explanation. Time spent making meaningful choices or fighting a hard wave doesn't automatically need shortening.

### Resource-band table (required)

"Reach" is the cheapest pull whose membrane touches the nearest glucose gem. "Burns" means a working mitochondrion, or glycolysis with none.

| State | What moves the student out, or ends the level |
|---|---|
| Objective 1, the chloroplast still drifting in | It homes on the centrosome and arrives without help; pulls toward it only speed that up. The mitochondrion runs the economy on the starting 50 G and the pool. |
| ATP below the Starvation Line, a working mitochondrion switched on, G ≥ 1 or a cycle under way | It credits ATP within one cycle. The warning may show briefly ([the timing ticket](../tickets.md#ticket-85)). |
| ATP below the Line, G = 0, a working chloroplast, a working mitochondrion on | The chloroplast's next glucose feeds the mitochondrion; the fat backup meanwhile if FA ≥ 1. |
| ATP below the Line, G = 0, FA ≥ 1, a working mitochondrion | The fat backup |
| ATP ≤ the tip threshold, the working mitochondrion **switched off** (the fat beat) | The **ATP tip** names Toggle (free); below the Line, the shared Starvation advice too |
| ATP below the Line, the working mitochondrion inside a digestion vesicle, not yet dead | Cancel Recycle (free), if its health is still at or above the stop threshold (Level 4) |
| From the cheapest pull up to the Line, G = 0, FA = 0, no chloroplast glucose coming, a working mitochondrion | Starvation is running. Pulls inside the spark roll for glucose while the pool lasts. Out by finding glucose, or **lost to Starvation**. |
| **Glucose pool empty** | Nothing by itself: the chloroplasts' glucose carries the economy while something burns it. Missed gems return +1 to the pool. |
| **No working mitochondrion, one still present (damaged)**, a working chloroplast | The chloroplast's glucose can't be burned and glycolysis is blocked. Out by an affordable **Divide** of the mitochondrion (a full-health daughter), or **recycling** the damaged one (100 ATP; glycolysis then runs on chloroplast glucose). Short of both: candidate W1 before the waves, or the waves on banked defences after. |
| No mitochondrion at all, a working chloroplast | Glycolysis, 2 ATP per G, on chloroplast glucose and the pool. Slow; Divide a mitochondrion is not possible (none to divide). |
| **No working chloroplast, one still present (damaged)**, Objective 3 unsatisfied | **Divide the damaged chloroplast** (no health check): a full-health daughter satisfies Objective 3. |
| **No chloroplast at all**, Objective 3 unsatisfied | ***No chloroplast left to divide*:** a Restart offer; a Dead End only under the full classification. |
| No chloroplast at all, Objective 3 satisfied | Winnable on the mitochondrion, the pool and banked resources |
| Divide a chloroplast with materials short (5 NT, 100 AA, 25 FA, 10 G) | AA from the pool; FA by surplus glucose (switch the mitochondrion off briefly) or recycling (a peroxisome, lysosome or membrane); the FA fix line on a refusal; NT from RNA breaking down |
| FA short for Objective 6 | Surplus glucose becomes fat (faster with the mitochondrion off); wear-out refunds; recycling |
| Auto-replace attempt unaffordable | The attempt is missed and reported; the missed count shows at the ER; buy one when affordable |
| Peroxisomes all worn out, auto-replace off | Buy peroxisomes (cap 10); peroxide meanwhile damages producers, lysosomes and the nucleus |
| A producer damaged by peroxide | Divide it (full-health daughter) or recycle it; DNA repair for the nucleus |
| Defensins short (Objective 8), ATP short | The chloroplasts' glucose through the mitochondrion; Take Defensin isn't available yet |
| NT short for an order (a closed budget) | Wait for RNA to break down; the honest temporary-cost refusal (Action Panel contract) |
| AA short for an order | The AA pool (regrowing), refunds, wave rewards; a partial fill buys what can be paid for (D18) |
| Nucleus damaged, ATP ≥ a DNA repair enzyme | Buy repair (reusable). Nucleus at 0: **lost**. |
| Membrane damaged, ATP ≥ the repair cost | Repair spends ATP and can push it below the Line. Below the repair cost, repair pauses, and further entries and exits risk **lysis**. |
| A wave under way, no more defenders affordable | Existing lysosomes (not used up), slicers and fused defensins may be enough; Take Defensin (wave 2) returns building blocks. If they aren't enough, the attempt ends in **lysis or Starvation**; candidate W2 only when no functioning defence exists or can come. |
| At the dish's edge | Inward pulls work; a roll outside the dish makes no gem and costs the pool nothing (D15) |

### Balance Runs

**The autopilot's declared outcome:** a **win**, with the Level Rating calculated correctly from its two Wave Ratings. Its time and rating are **reported**, not asserted, until calibrated. The *Economy hypotheses* are reported, never asserted.

Each run separates what it **asserts** from what it **reports**:

1. **Competent.** Asserts the win and a correct rating calculation. Reports play time, rating, ATP over time, glucose from the pool versus from chloroplasts, peroxisomes worn out and replaced, replacements missed, lysosomes lost to peroxide, and the defensin strength when each wave arrives.
2. **Mitochondrion left off.** Switches the mitochondrion off at the fat beat and never back on. Asserts the ATP tip shows when ATP reaches the threshold, re-arms after a switch-on, and never shows while every working mitochondrion is on. Reports the outcome.
3. **Last chloroplast recycled.** Reaches the example. Asserts the predicate on the tick it dies, the Restart offer, and no Dead End while the mitochondrion can make peroxide.
4. **Recovery fixtures for *No chloroplast left to divide*.** Each asserts the predicate stays false:

   | Fixture | State | Also asserts |
   |---|---|---|
   | **C1** | Objective 3; the only chloroplast inside a digestion vesicle, not yet dead | Cancel Recycle saves it; Divide on it gives a daughter; the autopilot wins |
   | **C2** | Objective 3; a Divide accepted and its daughter pending; the parent then recycled | The daughter appears and satisfies Objective 3 |
   | **C3** | Objective 3 satisfied, its minimum beat running; every chloroplast then recycled | The predicate stays false; Objective 3 completes; the level stays winnable |
   | **C4** | Objective 3; the only chloroplast damaged below the stop threshold | Divide on the damaged one gives a full-health daughter; the autopilot wins |

5. **Dead End classification.** From the example: with the mitochondrion only switched off, asserts the Restart offer (the nucleus route stays unknown). A variant where the mitochondrion is also recycled, every peroxide has settled and ATP is at or above the Line asserts the Dead End; the same with ATP below the Line asserts the Restart offer, reclassified to a Dead End if glycolysis brings ATP back to the Line.
6. **The validation task's candidates** (W1, W2) run under [their ticket](../tickets.md#ticket-99), not here.
7. **Auto-replace.** At the cap of 10 with the toggle on, a wear-out is replaced (D25 fixed). With ATP short, the attempt is missed, reported once, the missed count shows, there's no retry, and buying one clears the count. A player recycle is never replaced.
8. **The peroxide Objectives.** Objective 2 at 5 absorptions and Objective 4 at 10, each counted from its own arming; absorptions during a linger not carried over.
9. **Defensins.** Objective 7 on one accepted order; Objective 8 on the 5th **fusion**, not the 5th order; a student already at 5 completing after the introduction and minimum beat (D11 fixed); strength = fused ÷ nodes, capped; growth diluting it; wrapped viruses never rolling. Statistical: block rate against strength.
10. **The waves.** Asserts wave 2's first 7 are wrapped and re-attacks re-wrap ⌊pool × 0.5⌋; the Wrapped viruses hold opens only after wave 1's card; Take Defensin refused before Objective 10's arming.
11. **The Class Minimum.** Asserts `class_minimum_reached` on the tick the Chloroplast hold is acknowledged (not at engulfment), once per save; not emitted on a Restart or replay once saved; **earned on a later attempt** after a Restart before the Chloroplast hold was acknowledged; saved before any later loss.
12. **The starting ribosomes.** Asserts 30 ribosomes at load, never trimmed, and every Buy Ribosomes refused "at maximum".
13. **Peroxide targeting.** Headless and statistical: with 1 mitochondrion, 1–3 chloroplasts, 3–10 peroxisomes and 0–10 lysosomes, the initial target shares match the weights **with D21's band kept**. These are selection shares, not hit shares.
14. **Resource acquisition.** A seeded play-through that empties the glucose pool, asserting no regrowth and no debit for a roll with no room (D15), and reporting the AA pool's regrowth.

The stuck-state search runs on every Level 5 sweep ([`levels.md`: Testing](../levels.md#testing)).

### Unit tests (core)

- the starting state declared in content: menus, units and resources the same on first entry, Restart and replay; **30 ribosomes kept** with the cap refusing only further purchases; actions refused before their Objective is armed, including during the previous completion linger (chloroplast Divide, Auto-replace, Make Defensin, Take Defensin); fast-forward never enabling an action
- the chloroplast: placed when the Start hold closes, at the float-in distance, homing; engulfed at full health; Objective 1 at chloroplasts 0/1
- the Class Minimum: emitted on acknowledging the Chloroplast hold, not at engulfment, not before; once per save; persisted at once
- the hold gap between the Chloroplast and Sunlight and peroxide holds, with play running
- chloroplast production: 1 G per cycle, nothing charged, stopping when switched off, damaged, dividing or in a digestion vesicle; sunlight scaling the counting part only; stopped at sunlight 0 (the cycle length itself after [its ticket](../tickets.md#ticket-100))
- chloroplast peroxide: none on the first cycle, 1 per cycle after, emitted at the cycle's start; the chloroplast's targeting weight
- chloroplast Divide: cost and time; no health check; a full-health daughter; the parent's health kept; the cap counting damaged chloroplasts; refused while already dividing; Objective 3 at 1/2
- chloroplast recycling through Level 4's system; the charge, the refund once after the recycle animation; Cancel Recycle keeping acid damage
- the predicate: each condition just true and just false (a chloroplast in a vesicle, a daughter pending, Objective 3 satisfied but in its minimum beat, a damaged chloroplast); stays true once true; classification with and without a working mitochondrion, with and without peroxide in flight, at ATP 9 and 10
- the nucleus Loss Route's three answers, with chloroplasts as producers
- the exhaustion fact: a working chloroplast counting only when something can burn its glucose (a damaged mitochondrion present blocks both burning and glycolysis)
- auto-replace: off at load; one full-price attempt per wear-out; none for a player recycle; D25 (the cap at 10); the wear-out refund credited before the attempt's affordability check (an attempt short only of the refunded AA or FA succeeds); a missed attempt reported once and counted; no retry; the count cleared by a purchase
- the ATP tip: shown at ATP 500 and not at 501, only with a working mitochondrion switched off; re-arming after a switch-on; cleared when the condition ends
- the FA fix line on every FA refusal
- Objective 6 at FA 9/10, including the already-held case
- defensins: cost, cap counting ordered units; strength at 4, 5, 7 and 8 fused on 15 nodes, and after growth; one roll per unwrapped arrival; wrapped arrivals skipping it with no entry wound but exit damage; Take Defensin's charge, refund and immediacy
- Objective 8 at 4/5 fused with orders pending
- wave 2's wrap count (7 of 15) and the re-attack wrap
- the wave rewards credited exactly once each, on acknowledgement
- the unit conversion: the chloroplast float-in and virus distances over 30 Baseline frames equal the Baseline's

### Defect Patches

| Defect | Which test must fail |
|---|---|
| The Class Minimum emitted at engulfment | Script 11 and the Class Minimum unit test |
| The Class Minimum emitted again on a Restart | Script 11 |
| The Class Minimum not earnable after a Restart before it was reached | Script 11 |
| The starting ribosomes trimmed to the cap | Script 12 and the starting-state unit test |
| The chloroplast cycle hard-coded to 30 frames ("once a second") | The chloroplast production unit test, once [its ticket](../tickets.md#ticket-100) sets the value |
| D25 reproduced (replacement ordered before the dead unit leaves the count) | Script 7 and the auto-replace unit test |
| The replacement attempted before the wear-out refund is credited | The auto-replace unit test |
| Auto-replace retrying a missed attempt | Script 7 |
| Auto-replace replacing a player-recycled peroxisome | Script 7 |
| The ATP tip shown while the mitochondrion is on | Script 2 and the ATP tip unit test |
| Objective 8 counting orders instead of fusions | Script 9 |
| D11 reproduced (Objective 8 waiting for a further fusion) | Script 9 |
| A wrapped virus rolling against defensins, or making an entry wound | Script 9 and the defensin unit tests |
| Wave 2 wrapping 8 instead of 7 | Script 10 |
| The predicate ignoring a chloroplast in a digestion vesicle | Fixture C1 |
| The predicate ignoring a pending daughter | Fixture C2 |
| The predicate reading "Objective 3 not completed" instead of its satisfaction state | Fixture C3 |
| Divide refused on a damaged chloroplast | Fixture C4 |
| A Dead End declared while a producer can still make peroxide | Script 3 |
| The exhaustion fact counting a working chloroplast while a damaged mitochondrion blocks every burn | The exhaustion-fact unit test |
| Take Defensin enabled before Objective 10 | The action-refusal unit test and script 10 |

## Appendix

### Deviations

| Deviation | Pedagogy Rationale |
|---|---|
| **The ATP tip shows only while a working mitochondrion is switched off, and re-arms** (Baseline: once, at the first ATP change at or below 500, whatever the mitochondrion's state) | Advice must be true when it shows: the Baseline's line can tell a student to switch on a mitochondrion that is already on. Forgetting to switch it back on after the fat beat is the likeliest self-inflicted wall in the level the class must finish. A presentation lever, with no Tuning change. |
| **The two peroxide chains are two counted Objectives** (Baseline: 15 one-step Objectives) | One clear task each, with visible progress; the same completions. |
| **State predicates** for Objectives 1, 3, 5, 6 and 8, and **actions enabled when their Objective is armed** (Baseline: counts on events, D11's miscount, actions granted at the preceding tutorial) | Work that lands during a completion linger isn't lost; the same fix as Levels 2–4. |
| **The Baseline's per-Objective delays are replaced by the completion linger and the hold gap** | One shared pacing rule. The Baseline's second dialog keeps its own beat. |
| **The sunlight idea is shown in an illustrative demonstration** (Baseline: a line about adjusting the lamp, with no change) | The lesson must not depend on reading. Play stays at 100%, as in the Baseline. |
| **A missed auto-replacement is reported, with a count at the ER** (Baseline: a generic failure alert) | "It attempts to replace" is the true rule; showing the shortfall makes the ATP cost of peroxide visible instead of silent. The rule is unchanged. |
| **The shield is labelled as the chance to block an unwrapped virus** (Baseline: a percentage on shield icons) | Makes the wrapped-virus exception understandable, and keeps a game number from reading as biology. |

The Deviations shared by every level also apply, including **hydrogen peroxide** in place of "free radicals", **Nucleotides (NT)** in place of "NA", and the **Restart button** in place of the "screwed" dialogs (`docs/levels.md`).

**Not Deviations.** Recycle on the chloroplast from its arrival, Divide without a health check, the fixed glucose pool, the 30 starting ribosomes, Take Defensin only after wave 1, and wave 2's 7 wrapped are all **Baseline, kept on purpose**. Not declaring the unsent waves changes nothing a player sees.

### Fork defects this level touches

| Defect | Decision |
|---|---|
| **D1:** NT, AA and FA caps never apply | Kept uncapped (Baseline as played) |
| **D2:** 100% recycling refunds | Kept |
| **D5:** no cap on DNA repair | Kept (as Level 3) |
| **D10:** ignored level fields | The code default of 15 nodes is used; wave spread and delay ignored |
| **D11:** instant checks can't count defensins | **Fixed** by the state predicate on fused defensins (a clear failure). Checked in script 9. |
| **D15:** the pool loses a gem when there's no room | Fixed core-wide. It matters most here, on a fixed pool. Checked in script 14. |
| **D16 family:** off-by-one counters | The effective timings kept as Tuning |
| **D18:** partial batches | Kept, honestly reported (lysosome orders; defensins are 1 per order) |
| **D19:** nucleolus pores never returned | Unreachable: ribosomes start above the cap and can't be recycled |
| **D21:** the lysosome targeting band omits the peroxisome weight | **Kept as played** (the Level 4 decision); the controlled comparison is optional evidence under [Level 4's validation task](../tickets.md#ticket-94) |
| **D23:** worn-out and destroyed units refund | Kept (peroxisomes, lysosomes, as Levels 3–4) |
| **D25:** auto-replace ordered before the dead peroxisome leaves the count | **Fixed** (a clear failure), with the payment ordering preserved |
| **D26:** the FA tip fires on any FA refusal, all level | **Kept:** it is true every time, delivered as the refusal's fix line |
| **D27:** recycling the only chloroplast makes the level unwinnable | **Kept as Baseline freedom**; handled by the declared predicate and the Restart rule |
| **S1, S2:** the "no working mitochondrion / chloroplast" dialogs | Replaced by the shared Restart rule (`docs/levels.md`) |

Untouched: D3 (fixed in Level 3; Take Membrane uses the fix), D4, D6–D9, D12–D14, D17, D20, D22, D24 (fixed core-wide in Level 3).

### Content the fork declares but never uses

Recorded so nobody "restores" it later, thinking it was lost:
- **Two waves:** 20 injectors and 7 infesters. Nothing in the level file sends them. Not declared in our content.
- **NT and FA pools** (NT 50 at 0.05; FA 50 at 0.1), never switched on. Not declared.
- **The chloroplast's 25% peroxide chance** and its 45-frame starting period: dead code, never observable.
- **The sun-lamp line's adjustment:** no sunlight change follows it in the fork.

### Proofs

- **The chloroplast always arrives:** it homes on the centrosome at a positive speed until the membrane touches it, and nothing removes it before then.
- **The Class Minimum is reachable by every student who plays on:** the arrival needs no action, and the Chloroplast hold follows Objective 1's completion.
- **The scripted path is affordable from the start:** the Divide (200 ATP, 5 NT, 100 AA, 25 FA, 10 G) from the starting 1000 ATP, 550 AA, 30 FA and 50 G; 5 defensins (500 ATP, 250 AA, 5 FA, up to 5 NT in flight). The mitochondrion earns ATP throughout while it has fuel; the ATP margin depends on pulls, peroxisomes and replacements, which is candidate W1's question. FA is the tightest material before the Divide (30 held, 25 needed; each peroxisome bought first costs 2).
- ***No chloroplast left to divide* is irreversible:** see its section.

### Open items carried forward

- **The chloroplast cycle:** [Verify the chloroplast's production cycle](../tickets.md#ticket-100).
- **The mitochondrion cycle:** [the timing ticket](../tickets.md#ticket-85).
- **Candidates W1 and W2, the Loss Route unknowns, and the lysosomes needed for a chloroplast:** [the validation task](../tickets.md#ticket-99).
- **Saving the Class Minimum:** [the persistence extension](../tickets.md#ticket-101).

### For build slicing

This is the last spec before building starts (the map's *Sequencing* note). What the first slices need from it:
- **Starting-state declaration** must allow units above a purchase cap (30 ribosomes); the cap refuses purchases only. A content-validation rule that rejects "count > cap" would be wrong.
- **Chloroplast production** is blocked on [its timing ticket](../tickets.md#ticket-100); everything else about the chloroplast (arrival, Divide, recycling, peroxide targeting) is not.
- **Persistence** needs the Class Minimum field ([extension](../tickets.md#ticket-101)) before level select's Recap button.
- **Level-wide sunlight** is a core value from the first chloroplast level on; Levels 6–8 vary it.
- **Two holds between Objectives** (the hold gap) are a Story Hold sequencing case the core's hold machinery must support.
- **Honest refusals for automatic purchases** (auto-replace) reuse the Action Panel contract's refusal style without a player click.
- **Wrapped viruses and defensins** are shared virus systems Levels 6 and 8 reuse.

### Sources

- Fork slot 4's level file and source, read for this spec (clean room; nothing copied). The corrections found are recorded in `docs/research/baseline-mechanics.md` §K.
- Walkthrough `docs/baseline/walkthrough/level-5.md`. Its starting FA, G and peroxisome counts, its chloroplast menu (missing Toggle), its "sun lamp adjusted" and its "half" wrapped are misreadings or story (§K); the fork rules on numbers.
- Biology wording follows `docs/learning-targets.md`: light energy, not light, builds glucose; lipids are made from fatty acids; defensins are proteins; the endosymbiosis small print.
