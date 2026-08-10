# The TICM Framework

> This is the canonical specification of Threat-Informed Control Modeling. Every
> other document in this repository — the methodology, templates, skills, and
> worked examples — is an application of what is defined here. If something
> conflicts with this file, this file wins.

## 1. The one idea

**A control is a classifier.**

Every control sits on an *enforcement boundary* and sorts the events that cross
it into "allow" and "act on" (block, alert, slow, contain…). Like any
classifier, it makes two kinds of error:

- **False negatives** — harmful events it *should* have caught and didn't. These
  are the ways the control is **bypassed**. They are the risk source's story.
- **False positives** — legitimate events it *shouldn't* have interfered with and
  did. These are the ways the control **harms the organization**. They are the
  business's story.

Threat modeling only ever tells the first story, and usually only for one kind
of first story — an *adversary's*. Control catalogs (SOC 2, CIS, NIST) tell
neither — they assert a control should exist and stop there. **TICM's premise is
that you cannot model a control honestly without telling both stories, for
whichever kind of harm actually applies, at once** — because the same boundary
produces both error types, and — as we'll see in the Coupling Law (§8) — the
second error *manufactures* the first.

> **"Harmful," not "hostile."** Earlier TICM drafts used adversary-only language
> throughout ("hostile crossings," "the adversary's story") because the
> classifier's *false negatives* are usually framed as attacks. But a false
> negative is any harmful event the control missed — and harm has more than one
> author. §5 makes this precise: the same classifier framing that models a WAF
> against an attacker also models an access review against **organizational
> entitlement drift**, with no attacker anywhere in the story.

This gives TICM four lenses, applied to every control, always together:

| Lens | Question | What it measures | TICM taxonomy |
|---|---|---|---|
| **Role** | What does this control *act on* — the attack graph, another control, or a decision? | The risk-effect pathway the control operates through | 3 Control Roles (§3) |
| **Function** | What does this control do about the harm — deny it, degrade it, catch it, contain it…? | The harmful crossings (false negatives = bypasses) | 7 Functions for Direct controls; a Prevent/Identify/Correct triad for indirect ones (§4) |
| **Risk Source** | *Who or what causes* the harm this control addresses? | Which population of "false negative" you're actually modeling | 4 Risk Sources (§5) |
| **Disposition** | What does this control do to the *organization*? | The legitimate crossings (false positives = harm) | 5 Control Dispositions (§6) |

> **A note on "classifier."** The classifier framing is exact for *Direct*
> controls that sit on a traffic-bearing boundary. For controls that act
> indirectly — on the reliability of *other* controls, or on the quality of
> *decisions* — the same two-error-population logic holds, but the "boundary" is
> the target control's operating envelope or the decision itself. §3 makes this
> precise. This is TICM's integration of FAIR-CAM, and it is what lets TICM model
> **every** risk-mitigating control, not only the ones pointed at an attacker —
> and §5's Risk Source axis is what lets it model every *kind of harm*, not only
> the intentional kind.

## 2. The TICM Control Grid

The Function and Disposition lenses form a grid. Every control has a
**signature**: a Role, one or more Function tags, and exactly one Disposition per
objective path it touches. You say them together — *"MFA is a **Direct
Deny/Tax** control"*, *"a blocking WAF rule with no exception process is **Direct
Deny/Distort**"*, *"config-drift detection on that WAF is a **Sustaining
Identify/Neutral** control"*. The Role + pairing, and where it lands on the grid,
is the mnemonic. There is no forced acronym on purpose (see the note on DREAD in
`docs/04-rightsizing.md`). The grid below is drawn for **Direct** controls; §3
explains how Sustaining and Informing controls use the same grid with a
Prevent/Identify/Correct Function set.

**The signature's Disposition slot is the worst across paths.** A control
almost always touches more than one objective path, each with its own
Disposition (§6) — the signature shown in a grid cell or spoken aloud names
the single **worst** Disposition among them (Enable best, Block worst), never
an average. The full per-path table stays the authoritative record; the
signature is the headline, not a replacement for it.

```
                 ORGANIZATION-FACING  ──▶  Disposition
                 Enable   Neutral   Tax    Distort   Block
              ┌────────┬────────┬────────┬────────┬────────┐
   A   Deny   │  best  │        │        │  trap  │        │
   D  Degrade │        │        │        │  trap  │        │
   V  Detect  │        │        │        │  trap  │        │
   E  Deceive │        │        │        │  trap  │        │
   R  Contain │        │        │        │  trap  │        │
   S   Evict  │        │        │        │  trap  │        │
   A  Restore │        │        │        │  trap  │        │
   R          └────────┴────────┴────────┴────────┴────────┘
   Y-FACING ──▶ Function
```

- The **Enable** column is where good GRC engineering lives: real threat
  mitigation that *also* advances an objective (SSO that both hardens auth and
  speeds onboarding).
- The **Distort** column is the trap the rest of the field can't see, in *every*
  Function row: strong adversary-facing Function, but the control pushes people
  off the sanctioned path into shadow IT — which is often *worse* than Block
  because it's invisible (§6, §8). No Function is immune; a control being good at
  its job against the attacker says nothing about whether the org routes around
  it.
- Grid position predicts the **Rightsizing verdict** (§7). A `Deny/Enable`
  control is almost always Rightsized; a `Deny/Distort` control on a
  business-critical path is almost always **Misfit** no matter how strong its
  Function — the verdict TICM adds specifically to name "great against the
  threat, bad for the business."

## 3. The Role axis — what a control acts on (the FAIR-CAM integration)

A threat model points at attackers, so it is tempting to model only the controls
that point back. But most of a real control environment does not touch an
attacker directly. The control that catches your WAF silently flipped to
monitor-only never sees a packet from the adversary — it watches *another
control*. The threat-intelligence feed that tells you which technique to prioritize
never blocks anything — it shapes a *decision*. A control model that can't
represent those is not modeling the environment; it's modeling one slice of it.

TICM adopts the **FAIR Controls Analytics Model (FAIR-CAM)**'s central insight —
that controls affect risk through three distinct pathways — and makes it the top
of the model. (This is a credited adoption, not a TICM invention; see
`docs/07-control-roles-faircam.md` for the full mapping and citations. FAIR-CAM
V1.0, FAIR Institute, January 2025.) Every TICM control is first classified by
its **Role**:

| TICM Role | FAIR-CAM domain | What it acts on | Its Function sub-taxonomy |
|---|---|---|---|
| **Direct** | Loss Event Controls | The attack graph / the asset itself — it changes the frequency or magnitude of a loss event | The 7 Functions (§4): Deny · Degrade · Detect · Deceive · Contain · Evict · Restore |
| **Sustaining** | Variance Management Controls | *Another control* — it keeps other controls reliable, catching and correcting their degradation | Prevent · Identify · Correct **variance** (§4.1) |
| **Informing** | Decision Support Controls | *A decision or decision-maker* — it improves the quality and alignment of risk decisions | Prevent · Identify · Correct **misaligned decisions** (§4.2) |

**Why this keeps TICM threat-informed rather than diluting it.** A Direct control
is anchored to a threat. A Sustaining control is anchored to the *reliability of a
threat-facing control*. An Informing control is anchored to the *quality of a
threat-relevant decision*. Everything still traces back to the loss events that
threats cause — TICM just now models the indirect paths too, which is what
FAIR-CAM calls the "complete system view." The threat is still the root; Roles
are how far from it the control sits.

**Why this matters for the rest of the model.** The Role you assign changes what
"Coverage" and "Efficacy" mean in Rightsizing (§7): a Sustaining control's Force
is measured against the *variance it manages*, not the attack graph. And it gives
the Coupling Law (§8) its mechanism — **Distortion is a variance event**, so the
controls that catch coverage decay are exactly the Sustaining/Variance-Management
controls. The Disposition (§6) and Assurance (§9) lenses apply unchanged to all
three Roles: a control-monitoring Sustaining control can itself be a Tax or a
Distort on the ops team, and it too must be Designed/Implemented/Operating.

## 4. The Function axis — what a control does about the harm

Seven control functions. Each is defined by (a) the change it makes to the
harmful event's trajectory, (b) the single variable it moves, and (c) a
**falsification test** — a test that must behave a specific way or the tag is a
lie. The set is a deliberate synthesis of the Lockheed-Martin kill-chain
Courses-of-Action and MITRE D3FEND's tactics (see `docs/06-prior-art.md`); TICM
does not claim it as novel, and it is designed to *crosswalk* to ATT&CK/D3FEND,
not fork them. The seven also map cleanly onto FAIR-CAM's Loss-Event-Control
sub-functions — Prevention (Deny/Degrade/Deceive), Detection (Detect), Response
(Contain/Evict/Restore) — which is why they cover the whole loss-event
lifecycle, not just prevention.

> **Function is agnostic to Risk Source.** Deny/Degrade/Detect/etc. were
> originally written with an adversary in mind — kill-chain and D3FEND are both
> adversarial-only frameworks — but nothing in the mechanism actually requires
> intent. "Deny" removes an edge whether the thing that would have crossed it is
> an exploit or a fat-fingered `DROP TABLE`. §5 names *what kind of thing* was
> being denied; this section names *what the control did about it*, and stays
> the same regardless. The falsification test's evidence type follows the Risk
> Source (§5) — adversarial claims get emulated-attack evidence; the other three
> get the evidence types §5 defines.

| Function | What it changes | Bound variable | Falsification test |
|---|---|---|---|
| **Deny** | Removes an attack-graph edge or makes a technique precondition unsatisfiable | P(success \| attempt) → 0 for that edge | Emulated attempt fails with **no defender action required** |
| **Degrade** | Raises the cost/time/reliability of an edge without removing it | Adversary cost/time; P(success) < 1 | Measured drop in technique success rate or speed under emulation |
| **Detect** | Makes a crossing observable **and hands it to a named responder** | Time-to-detect / time-to-respond | Emulation fires an alert that is **actually triaged** by the declared consumer |
| **Deceive** | Corrupts the adversary's *model* of the graph | Adversary information quality | Adversary acts on false state (decoy-interaction telemetry) |
| **Contain** | Bounds the reachable set *after* a foothold | Blast radius / lateral reach | Blast-radius delta measured under emulation |
| **Evict** | Removes established adversary occupancy | Persistence survival | Post-eviction persistence re-check fails |
| **Restore** | Shrinks the **magnitude or duration of loss** from an event that already happened | Loss magnitude / time-to-restore | Recovery drill under emulated destruction meets the stated objective (RTO/RPO) |

**Boundary rules (what each is NOT):**

- **Deny is not Degrade.** If a motivated adversary can still traverse the edge
  at higher cost, it's Degrade. Deny means the edge is *gone*.
- **Detect is not Deny.** A sensor that also blocks is *two* tags (Deny + Detect),
  not one. And **a detection nobody triages is a log, not a control** — Detect
  requires a declared response consumer or it fails its test.
- **Deceive is not Detect.** Deceive operates on the adversary's *beliefs*; a
  canary that alerts is Deceive **+** Detect.
- **Contain is not Deny.** Contain acts *post-compromise*; Deny acts on the edge
  before it's crossed.
- **Evict is not Restore.** Evict removes the adversary's presence; Restore
  returns the asset or service to a known-good state and shrinks what the
  incident cost. A ransomware wipe is *Evicted* by killing the foothold and
  *Restored* by recovering from backup — two functions, often two controls.
- **Restore (Function) is not Enable-Resilience (Disposition).** This pair is
  the subtle one, and getting it right is what lets TICM model a backup control.
  *Restore* is the **loss-facing** effect: the breach costs less because you
  recovered fast (bound variable: loss magnitude / time-to-restore, scored
  against the threat). *Enable-Resilience* is the **objective-facing** effect:
  the business kept running (scored on the Disposition axis, §6). A backup/DR
  control is typically tagged **both** — `Restore` on the Function axis and
  `Enable` on the Disposition axis. Keeping them separate is what makes a
  resilience control rightsizable against the threat instead of vanishing into a
  business-continuity footnote. (Earlier drafts folded restoration entirely into
  Enable; that left pure loss-magnitude controls with no Function and no way to
  measure their Force — so Restore earns its place as a distinct Function.)

**Coordinates, not categories.** A control's kill-chain stage and its ATT&CK
technique IDs are *coordinates the function is applied at*, not additional
functions. "Deny at T1110 (Brute Force)" is a Deny control with a coordinate.
This is the seam that keeps TICM interoperable with ATT&CK and D3FEND instead of
competing with them. Full treatment: `docs/02-taxonomy-function.md`.

### 4.1 The Function set for Sustaining (Variance Management) controls

A Sustaining control's "adversary" is **variance** — the drift, decay, and
silent failure of another control. Following FAIR-CAM's Variance Management triad,
its Functions are:

| Function | What it does to variance in the target control | Falsification test |
|---|---|---|
| **Prevent (variance)** | Stops the target control from drifting out of its designed state | Inject a drift attempt (config change, disable) → it is refused or auto-reverted |
| **Identify (variance)** | Surfaces that a target control has degraded, **and hands it to a named responder** | Degrade the target under test → an alert fires and is triaged |
| **Correct (variance)** | Returns a degraded target control to its designed state | After a detected degradation, the target is restored and re-validated |

Examples: change-management gates (Prevent), config-drift detection and control
testing (Identify), automated remediation / IaC reconciliation (Correct). The WAF
that gets flipped to monitor-only is caught by a Sustaining **Identify** control —
and, per the Coupling Law (§8), that is the same machinery that catches
Distortion-driven coverage decay.

### 4.2 The Function set for Informing (Decision Support) controls

An Informing control's target is a **decision** — its failure mode is a
*misaligned decision* (the wrong control funded, the wrong risk accepted, the
wrong technique deprioritized). Following FAIR-CAM's Decision Support triad:

| Function | What it does to decision quality | Falsification test |
|---|---|---|
| **Prevent (misalignment)** | Shapes decisions toward alignment before they're made (expectations, incentives, policy, training) | A decision scenario is put to the population → the aligned choice is the default |
| **Identify (misalignment)** | Surfaces that decisions are drifting from intent (metrics, risk reporting, audit findings) | Introduce a misaligned decision → reporting flags it to an owner |
| **Correct (misalignment)** | Realigns decisions and the incentives behind them (feedback loops, accountability) | A flagged misalignment produces a changed decision, not just a logged one |

Examples: threat intelligence and risk assessments (Prevent — they set the
expectations a good decision starts from), security metrics and KRIs (Identify),
incident retrospectives that change policy (Correct). Informing controls are how
an organization stays threat-*informed* over time; they are first-class controls
in TICM, not "governance overhead."

> **The whole model in one line:** *Role* says which pathway a control works
> through; *Function* says what it does to its target on that pathway; *Risk
> Source* says what kind of harm it's actually defending against; *Disposition*
> says what it costs the organization; *Rightsizing* says whether the trade is
> worth it. Every risk-mitigating control — a firewall rule, a control-monitoring
> job, an access review, or a threat-intel subscription — gets all five.

## 5. The Risk Source axis — what kind of harm the control addresses

Every Function tag (§4) is a claim about stopping, catching, or containing
*something harmful*. TICM has, until now, let "something harmful" default
silently to "an attacker" — because the Function set was synthesized from two
frameworks (kill-chain, D3FEND) that only model adversaries. That default is
why access reviews, change-approval gates, and policy controls have always felt
like a stretch to fit into a "threat" model: their harm isn't an attacker at
all, and forcing adversary language onto them (an "attack emulation" for a
quarterly access review) produces exactly the un-emulatable-control problem
`docs/04-rightsizing.md`'s alternate-evidence tier had to patch around.

TICM fixes this at the root by adopting **NIST SP 800-30**'s threat-source
taxonomy — a standard risk-assessment framework, not a TICM invention — as an
explicit fourth axis, credited and cited
(<https://csrc.nist.gov/pubs/sp/800/30/r1/final>; corroborated across
independent secondary sources describing its Appendix D taxonomy — the primary
PDF was not directly rendered this session, which is disclosed rather than
papered over). Four risk sources, closed set:

| Risk Source | Who or what causes the harm | Example | Falsification evidence |
|---|---|---|---|
| **Adversarial** | An intentional actor exploiting a vulnerability for gain | An attacker exfiltrating customer data | Emulated attack (§4's default) |
| **Accidental** | An authorized person making an honest mistake | An admin fat-fingers a firewall rule; an employee mis-sends a file | Injected-error scenario: does the control catch a realistic honest mistake? |
| **Structural** | A system — technical or organizational — drifting or degrading on its own over time | Software/hardware aging; **entitlements drifting from role as people change jobs and nobody removes the old access** | Injected-drift scenario: does the control catch state that has silently diverged from its designed baseline? |
| **Environmental** | Forces outside anyone's control | Fire, flood, power failure, regional outage | DR/continuity drill under simulated disaster |

**Why this isn't a new taxonomy competing with Function — it's what Function is
*about*.** Risk Source and Function answer different questions and mostly
combine freely: a **Deny** control can Deny an adversary (a WAF rule) or Deny
an accident (a DB constraint that rejects a query with no `WHERE` clause). A
**Restore** control can Restore after ransomware (Adversarial) or after a
datacenter fire (Environmental) — same Function, different Risk Source, same
falsification *shape* (a recovery drill), different scenario injected.
"Mostly," not "every cell populated": **Deceive** presupposes an intentional
occupant to mislead — you cannot corrupt the mental model of a flood or a
fat-finger — and **Evict** presupposes an intentional occupant to remove.
Those two are Adversarial-only by construction; the other five Functions
generalize across all four Risk Sources.

**This is what finally makes "good governance" controls first-class citizens.**
An access-recertification control was always awkward in a pure attacker-model —
its real job is catching **Structural** drift (entitlements that no longer match
the role), which is precisely why it landed on the **Sustaining** Role (§3):
Sustaining exists to manage variance, and **Structural is the typical cause** of
the variance a Sustaining control manages — though not the only one; a Sustaining
control can just as well be watching for Adversarial variance (an attacker
tampering with another control) or Accidental variance (a one-time
misconfiguration). Naming *which* is exactly what the Risk Source axis is for.
The same logic extends the whole
governance surface: a data-classification policy's harm is mostly **Accidental**
(people mishandling data because expectations were never clear); a documented
asset inventory's harm is **Structural** (nobody knows what to protect once the
map drifts from reality). None of these needed a new Role or a new Function —
they needed the honest name for the "threat" they were always defending
against, which TICM's adversary-only vocabulary had been hiding.

**Notation.** Because Adversarial has been TICM's implicit default from the
start, it stays unlabeled in a control's signature for brevity — `Direct ·
Deny+Detect · Tax` still means Adversarial unless stated. Non-adversarial risk
sources are named explicitly: `Sustaining · Identify(Structural) · Neutral`. A
control can carry more than one Risk Source (a WAF is mostly Adversarial but
also Accidental against a misconfigured client retry-storm); name each one it
genuinely claims coverage for, no more.

**What this buys the reactive-assessment method** (`methodology/
reactive-assessment.md`): a genuinely new coverage question most
organizations never ask explicitly — *do we have adversarial coverage AND
non-adversarial coverage on this asset, or only the kind of "security" that
implies an attacker?* Most control inventories are adversary-only by the same
silent default TICM just named and removed.

## 6. The Disposition axis — what a control does to the organization

This is the axis no existing control-*modeling* method makes a first-class
categorical taxonomy, and it is where most of TICM's novelty lives. (The
underlying behavior — friction breeding workarounds — is well documented in the
usable-security literature; what's new is treating it as a modeled per-control
category. See `docs/06-prior-art.md`.) Five dispositions. Each is a **categorical
mechanism** (not a severity score), assessed **per named objective path** and
**per subject
population** — never as one global label, because the same control can Enable
one objective and Block another.

Ordered by their effect on the organization's ability to achieve the objective,
best to worst:

| Disposition | Mechanism | Boundary test (how you know it's this one) |
|---|---|---|
| **Enable** | The control *advances* the objective. Absorbs two subtypes: **Assure** (evidence the control produces unlocks value — the SOC 2 report that closes the enterprise deal) and **Resilience** (the control restores the objective flow after disruption — backups, failover). | The objective is measurably *better off* with the control than without it. |
| **Neutral** | No measurable effect on any enumerated objective path. A verdict *earned by enumeration*, never by not looking. | You listed the objective flows crossing this boundary and none moved. |
| **Tax** | A cost (time, latency, dollars) is paid, but the population **stays on the sanctioned path**. | Measurable overhead **and** no route-around *pattern*. |
| **Distort** | The control induces **circumvention** — people route *around* the sanctioned path (shadow IT, shared credentials, standing exceptions, personal devices). | Route-around behavior exists as a **pattern** — observed or confidently predicted, not a one-off. |
| **Block** | The sanctioned path is **severed** — the objective is unachievable through it. | The objective cannot be completed via the approved route at all. |

**The two dispositions people get wrong:**

- **Distort is not high Tax.** Tax and Distort are different *kinds*, not two
  points on a dial. The boundary is behavioral: *did a route-around pattern
  emerge?* Tax is friction people absorb; Distort is friction people escape. A
  path is Distort once circumvention is a recurring pattern — one frustrated
  employee improvising once is noise, not a classification. (The line is
  *pattern*, deliberately, so the category is a real kind and not a hair-trigger.)
- **Distort is often worse than Block.** Block is loud and visible — the work
  stops, someone escalates, you find out. Distort is silent — the work continues
  on an unmonitored path you didn't sanction and can't see. **Every Distort
  rating auto-generates an entry in the control-bypass threat model** (§9),
  because a workaround is new attack surface by definition.

> **Classification vs. consequence — the rule that keeps Distort honest.**
> *Classifying* a path as Distort (there is a route-around pattern) is not the
> same as *vetoing* the control. The hard consequences — the deploy veto (§7) and
> the Operating-without-Distortion failure (§9) — fire only on a **material,
> unmitigated** Distort: *material* = circumvention above a stated threshold on a
> path that matters, and *unmitigated* = there is no exit ramp (Tunability) **and**
> no Sustaining control catching and correcting the drift (§3). A Distort with a
> sanctioned exception process and a control-monitoring job behind it is still a
> Distort — you still model its bypass surface — but it is a *managed* one, not an
> automatic veto. This is the join between the categorical boundary (existential,
> pattern-based) and the assurance threshold (quantitative): the first says what
> kind of effect it is; the second says whether it's bad enough to stop you.

**Objective-Path Analysis.** You cannot assign a Disposition without first
enumerating the organization's objective flows the way a threat model enumerates
attack paths: revenue capture, deploy velocity, the customer journey, hiring,
support resolution. Each control is then placed on the objective paths it
intersects, and given a Disposition *per path with a named bearing population*.
"This control is a Tax" is noise; "this control is a Tax on the deploy-velocity
path borne by the 40-person platform team, ~6 min/deploy × ~200 deploys/week" is
a finding. Full treatment: `docs/03-taxonomy-disposition.md`.

## 7. Rightsizing — qualifying the control

Categorizing a control (its grid signature) is half the job. **Qualifying** it —
deciding whether it's the *right* control, *right-sized* for this organization
against these threats — is the other half, and it is where TICM cashes out its
promise that "a strong control can still be a bad control."

Rightsizing weighs a **Force** ledger (adversary-facing) against a **Drag**
ledger (organization-facing), each dimension anchored to an observable, and
produces one of four **verdicts** — deliberately a verdict, not a number, and
deliberately not an acronym:

- **Force:** Efficacy (does the claimed Function pass its falsification test
  under emulation), Coverage (fraction of the modeled attack paths this control
  touches), Bypass-resistance (cost of the adversary's cheapest way around it).
- **Drag:** Friction (the Tax magnitude on each named objective path),
  Distortion-pressure (observed or predicted circumvention rate — the single
  most security-relevant drag signal, because of the Coupling Law), Sustainment
  (upkeep + triage burden + the control's own fragility as a single point of
  failure).

| Verdict | Meaning |
|---|---|
| **Rightsized** | Force materially exceeds Drag on **every** intersected objective path, and there is an exit ramp (Tunability). |
| **Oversized** | Force exceeds the modeled threat this control was deployed to address; Drag is unjustified. Turn it down, scope it, or stage it. |
| **Undersized** | Drag is paid but Force is insufficient for the modeled threat. Strengthen it or stop paying for it. |
| **Miscast** | Wrong **Function** for the threat entirely — an *adversary-side* kind error. No amount of tuning fixes it; replace it. |
| **Misfit** | Right Function and *sufficient* Force against the threat, but a **material, unmitigated Distort or Block** on an intersected objective path makes deploying it net-negative — an *organization-side* kind error. This is the verdict TICM adds to name the thesis directly: **great against the attacker, bad for the business.** |

**Miscast and Misfit are the matched pair** that make "a strong control can be a
bad control" precise: Miscast fails on the *adversary* side (wrong tool for the
threat), Misfit fails on the *organization* side (right tool, but its Drag
disqualifies it). Both are kind errors; neither is fixed by turning a dial.

**Precedence rule (resolves the Oversized/Undersized ambiguity).** Always score
Force against **the modeled threat the control was deployed to address**, not an
unrelated threat it happens to also touch. A control that is strong against
something nobody is defending here, and weak against the thing that is actually
in scope, is **Undersized** (for the modeled threat) — not Oversized (for the
incidental one). Name the threat first; then judge the fit.

**Two hard gates**, independent of the Force score:

1. **Tunability is the deploy gate.** If the control cannot be scoped, staged,
   or relaxed, it has no exit ramp — *no exit ramp, no deploy.*
2. **A material, unmitigated Distort or Block on a critical path is a veto** →
   the control's verdict is **Misfit**, and it does not deploy as-is **regardless
   of how strong the Function is.** "Material + unmitigated" is defined in §6:
   circumvention above a stated threshold on a path that matters, with no
   exception ramp and no Sustaining control catching it. This is the rule that
   operationalizes "a control that mitigates threats but disrupts objectives is a
   bad control" — and the fix is never "ship it anyway," it's retune the control
   until the Distort clears or add the exit ramp / Sustaining control that
   downgrades it to managed.

Organizations with the measurement maturity to do so can deepen the same ledger
into a quantitative **Control Net Value** (a FAIR-CAM-style
ΔALE × coverage × reliability − net-drag, with calibrated intervals). CNV is the
ceiling, not the entry bar — TICM is designed so a team can run the verdict
bands on day one with observables a SOC already produces. Full rubric:
`docs/04-rightsizing.md`.

## 8. The Coupling Law — why the Function and Disposition axes are one system

This is TICM's signature claim, and the thing that makes the Disposition axis a
security concern rather than a UX footnote:

> **Distortion decays Coverage.** Every Distort disposition generates
> circumvention; every circumvention is a legitimate flow that has *left the
> enforcement boundary*; and a flow that has left the boundary is (a) — where the
> control carries a Detect function — invisible to it, and (b) now outside this
> control's management, on a path this control was never measured against. So a
> control's organization-facing failure directly **erodes its own
> adversary-facing Coverage** — and the Coverage number you measured before the
> workaround appeared is now stale.

Formally, in the classifier framing (§1): a false positive severe enough to
provoke a workaround *removes that flow from the population the classifier sees*.
It can never again show up as *this control's* false positive — the flow now runs
outside this control's boundary, unmanaged by it, and unknown until someone
re-enumerates the paths. It may still meet other controls (defense in depth is
real), but *this* control's Coverage is now less than its model claims, and the
new path is unmodeled until you look. **Friction converts into attack surface the
model can't see.** That is why Distortion-pressure is weighted so heavily in the
Drag ledger, and why every Distort rating writes an entry into the bypass threat
model. The behavioral half of this loop is old news to the usable-security
literature (Sasse's compliance budget, "shadow security" — see
`docs/06-prior-art.md`); what TICM adds is making it a *modeled, per-control,
generative* property — coupled to Coverage, wired to auto-generate bypass
entries, and handed to the Sustaining/Variance-Management controls (§3) whose job
is precisely to catch this kind of drift.

## 9. The Assurance spine — designed, implemented, operating

Categorization and rightsizing describe the control *as intended*. The assurance
spine asks whether it's *real*. TICM borrows the auditor's triad (this is openly
SOC 2's language, not a TICM invention) and grounds each tier in evidence:

- **Designed effectively** — would the control's dependency graph, *if fully
  true*, actually close the attack paths it claims to? (An architecture review:
  did you even account for the origin-IP-disclosure path?)
- **Implemented effectively** — is each dependency *true right now*? (Point-in-time
  evidence: DNS export, firewall rule export, policy API response.)
- **Operating effectively** — does it *stay* true, and would you know the moment
  it stopped? TICM sharpens this beyond the audit sense with two requirements:
  it must be **validated against its falsification test**, with the evidence
  type following the Risk Source it claims (§5) — adversary emulation for an
  Adversarial claim, an injected-error/drift/disaster scenario for the other
  three (this is *why* the alternate-evidence tier in `docs/04-rightsizing.md`
  exists: an un-emulatable control usually just has a non-Adversarial Risk
  Source and was being held to the wrong evidence standard) — and it must be
  **Operating without material Distortion** — circumvention held below its
  stated threshold, or caught and corrected by a Sustaining control (§3). A
  control carrying a material, unmitigated Distort is *not* operating
  effectively, however green its config, because per the Coupling Law its real
  Coverage is decaying under it.

Each control's dependencies are enumerated in a checkable schema —
**Enablement** (must exist to function at all), **Routing** (ensures traffic
actually passes through it), **Enforcement-boundary** (prevents it being routed
around) — and each becomes a monitorable assertion. The false-negative side of
those dependencies is the **control-bypass threat model**: the same diagramming
discipline as a threat model, but the asset under attack is *the control itself*.
Full treatment: `docs/05-assurance-spine.md`.

## 10. What is genuinely new here (and what isn't)

TICM is a synthesis, and honesty about the seams is part of the method:

- **Borrowed, not claimed:** the **Role axis** (Direct/Sustaining/Informing) is
  FAIR-CAM's Loss-Event / Variance-Management / Decision-Support model, adopted
  wholesale and credited (FAIR-CAM V1.0, FAIR Institute, Jan 2025); the **Risk
  Source axis** (Adversarial/Accidental/Structural/Environmental) is NIST
  SP 800-30's threat-source taxonomy, adopted and credited — with one honest
  extension: NIST scopes Structural to technical-system failure, and TICM
  deliberately broadens it to organizational drift too (`docs/08-risk-source.md`
  states this plainly, it is not silent adoption); the Function
  axis for Direct controls (from kill-chain CoA + D3FEND); the Prevent/Identify/
  Correct sub-triads for Sustaining and Informing controls (FAIR-CAM); the
  assurance triad (from SOC 2); the quantitative Control-Net-Value deep tier
  (FAIR-CAM); the classifier framing (standard detection theory).
- **Genuinely new:** the **Disposition axis** as a first-class categorical
  taxonomy of a control's effect on organizational objectives; the **Distort**
  category and its elevation from a known usable-security *phenomenon* (Sasse's
  compliance budget, "shadow security" — credited in `docs/06-prior-art.md`) to a
  *modeled, per-control, generative* property; the **Coupling Law** linking
  organizational friction to a control's own coverage decay (and identifying it
  as a *variance event* that Sustaining controls exist to catch); putting the
  Risk Source axis (borrowed) and the Function axis (borrowed) *together* so
  neither is silently assumed adversary-only, which is what makes access
  reviews, policy controls, and DR drills first-class TICM controls instead of
  an awkward stretch; and the **Rightsizing verdict** — especially the matched
  pair **Miscast** (wrong tool for the threat) and **Misfit** (right tool, but
  its Drag disqualifies it) — as the qualification output. The one-line version:
  *TICM is the first control-modeling method that fits FAIR-CAM's complete-system
  view of control types and NIST's complete-system view of risk sources onto a
  threat-informed model, and makes a control's effect on the business a
  load-bearing, security-relevant part of it.* The honest scope of the novelty:
  the behavioral observation that friction breeds workarounds is decades old,
  and the adversarial/non-adversarial split is a standard risk-assessment
  distinction; making both a categorized, coupled, generative part of a
  per-control model is what's new.
- **The division of labor with FAIR-CAM:** FAIR-CAM answers *"how much does this
  control reduce risk, in units?"* — a measurement model. TICM answers *"what
  kind of control is this, against which threats, at what cost to the business,
  and is it the right one?"* — a **modeling and design** method. TICM uses
  FAIR-CAM's control-type structure as its Role axis and offers CNV as its
  quantitative ceiling; FAIR-CAM gains a threat-anchored, objective-aware design
  front-end. They compose; they do not compete.

See `docs/06-prior-art.md` for the point-by-point differentiation from STRIDE,
D3FEND, the kill-chain, FAIR-CAM, Attack-Defense Trees, and the control catalogs,
`docs/07-control-roles-faircam.md` for the full FAIR-CAM mapping, and
`docs/08-risk-source.md` for the full NIST SP 800-30 mapping and worked
examples across all four Risk Sources.
