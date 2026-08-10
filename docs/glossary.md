# TICM Glossary

Every TICM term in one place, alphabetized. Each entry gives a one- or
two-sentence working definition and a pointer to the document where it is
defined in full. When a short definition here and a full treatment disagree, the
full treatment — and above all [`01-framework.md`](./01-framework.md) — wins.

---

**Assurance spine** — The three-tier test of whether a control is *real*:
Designed, Implemented, Operating effectively. Borrowed openly from the auditor's
triad (SOC 2 language). See [`05-assurance-spine.md`](./05-assurance-spine.md).

**Attack-graph** — The set of edges (technique preconditions → effects) an
adversary can traverse to reach an asset. It is what a Direct control acts on,
and the thing Coverage is measured against. See [`02-taxonomy-function.md`](./02-taxonomy-function.md).

**Block** — The worst Disposition: the sanctioned objective path is *severed* —
the objective cannot be completed via the approved route at all. See [`03-taxonomy-disposition.md`](./03-taxonomy-disposition.md).

**Bypass-resistance** — A Force dimension: the cost of the adversary's *cheapest*
way around the control. See [`04-rightsizing.md`](./04-rightsizing.md).

**Contain** — A Direct Function that bounds the reachable set *after* a foothold;
it moves blast radius / lateral reach, not the initial edge. Not Deny (Deny acts
before the edge is crossed). See [`02-taxonomy-function.md`](./02-taxonomy-function.md).

**Control-bypass threat model** — A threat model whose asset under attack is *the
control itself* — the false-negative side of its dependencies. Every Distort
rating auto-generates an entry here. See [`05-assurance-spine.md`](./05-assurance-spine.md).

**Control Grid** — The Function × Disposition matrix a Direct control's
Function tags and Disposition are plotted onto; grid position predicts the
Rightsizing verdict. See [`01-framework.md`](./01-framework.md) §2.

**Control Role** — The top of the model: what a control *acts on* — the attack
graph (Direct), another control (Sustaining), or a decision (Informing). TICM's
FAIR-CAM integration. See [`07-control-roles-faircam.md`](./07-control-roles-faircam.md).

**Coupling Law** — TICM's signature claim: *Distortion decays Coverage.* A
false positive severe enough to provoke a workaround removes that flow from the
boundary, converting friction into unmanaged attack surface. See [`01-framework.md`](./01-framework.md) §7.

**Coverage** — A Force dimension: the fraction of modeled attack paths this
control actually sits on. For a Sustaining control, measured against the variance
it manages, not the attack graph. See [`04-rightsizing.md`](./04-rightsizing.md).

**Deceive** — A Direct Function that corrupts the adversary's *model* of the
graph, moving their information quality. A canary that also alerts is Deceive +
Detect. See [`02-taxonomy-function.md`](./02-taxonomy-function.md).

**Degrade** — A Direct Function that raises the cost/time/reliability of an edge
*without removing it*; a motivated adversary can still traverse it. See [`02-taxonomy-function.md`](./02-taxonomy-function.md).

**Deny** — A Direct Function that removes an attack-graph edge outright — an
emulated attempt fails with no defender action required. Not Degrade. See [`02-taxonomy-function.md`](./02-taxonomy-function.md).

**Dependency schema (Enablement / Routing / Enforcement-boundary)** — The three
checkable classes of precondition a control needs: Enablement (must exist to
function), Routing (traffic actually passes through it), Enforcement-boundary
(it can't be routed around). See [`05-assurance-spine.md`](./05-assurance-spine.md).

**Detect** — A Direct Function that makes a crossing observable *and hands it to
a named responder*; a detection nobody triages is a log, not a control. See [`02-taxonomy-function.md`](./02-taxonomy-function.md).

**Direct control** — A control acting on the attack graph or the asset itself,
changing the frequency or magnitude of a loss event; uses the 6 Functions.
FAIR-CAM's Loss Event Controls. See [`07-control-roles-faircam.md`](./07-control-roles-faircam.md).

**Disposition** — What a control does to the *organization* — the axis no prior
framework has. One of five, assessed per objective path and per subject
population. See [`03-taxonomy-disposition.md`](./03-taxonomy-disposition.md).

**Distort** — The Disposition where the control induces *circumvention* — people
route around the sanctioned path. Often worse than Block because it's silent.
See [`03-taxonomy-disposition.md`](./03-taxonomy-disposition.md).

**Distortion-pressure** — A Drag dimension: the observed or predicted
circumvention rate — the single most security-relevant drag signal, because of
the Coupling Law. See [`04-rightsizing.md`](./04-rightsizing.md).

**Drag** — The organization-facing ledger in Rightsizing: Friction,
Distortion-pressure, and Sustainment. Weighed against Force. See [`04-rightsizing.md`](./04-rightsizing.md).

**Efficacy** — A Force dimension: does the claimed Function pass its
falsification test under adversary emulation. See [`04-rightsizing.md`](./04-rightsizing.md).

**Enable** — The best Disposition: the control *advances* the objective (absorbs
the Assure and Resilience subtypes). Restoring service after Evict lives here.
See [`03-taxonomy-disposition.md`](./03-taxonomy-disposition.md).

**Evict** — A Direct Function that removes established adversary occupancy;
post-eviction persistence re-checks must fail. Not Recover (that's Enable). See [`02-taxonomy-function.md`](./02-taxonomy-function.md).

**Force** — The adversary-facing ledger in Rightsizing: Efficacy, Coverage,
Bypass-resistance. Must materially exceed Drag to be Rightsized. See [`04-rightsizing.md`](./04-rightsizing.md).

**Function** — What a control does to its *target* — the 6 adversary Functions
for Direct controls, a Prevent/Identify/Correct triad for indirect ones. See [`02-taxonomy-function.md`](./02-taxonomy-function.md).

**Identify (variance / misalignment)** — The indirect Function that *surfaces*
degradation and hands it to a named responder: variance in a target control
(Sustaining) or drift in decisions (Informing). See [`07-control-roles-faircam.md`](./07-control-roles-faircam.md).

**Informing control** — A control acting on a *decision or decision-maker* to
improve alignment; its failure mode is a misaligned decision. FAIR-CAM's Decision
Support Controls. See [`07-control-roles-faircam.md`](./07-control-roles-faircam.md).

**Loss Event / Variance Management / Decision Support (FAIR-CAM)** — The three
FAIR-CAM control domains TICM adopts wholesale as its Direct / Sustaining /
Informing Roles. See [`07-control-roles-faircam.md`](./07-control-roles-faircam.md).

**Miscast** — The Rightsizing verdict for the *wrong Function* for the threat
entirely; no amount of tuning fixes a kind error — replace it. See [`04-rightsizing.md`](./04-rightsizing.md).

**Neutral** — The Disposition of no measurable effect on any enumerated objective
path — a verdict earned by enumeration, never by not looking. See [`03-taxonomy-disposition.md`](./03-taxonomy-disposition.md).

**Objective-Path Analysis** — Enumerating the organization's objective flows
(revenue capture, deploy velocity, hiring…) the way a threat model enumerates
attack paths, so a Disposition can be assigned per path with a named bearing
population. See [`03-taxonomy-disposition.md`](./03-taxonomy-disposition.md).

**Operating effectiveness** — The Assurance tier asking whether the control
*stays* true and would signal the moment it stopped; TICM requires it be
adversary-emulation-validated and Operating without Distortion. See [`05-assurance-spine.md`](./05-assurance-spine.md).

**Oversized** — The Rightsizing verdict where Force exceeds the threat that
actually applies and the Drag is unjustified — turn it down, scope it, or stage
it. See [`04-rightsizing.md`](./04-rightsizing.md).

**Prevent (variance / misalignment)** — The indirect Function that *stops*
failure before it happens: keeps a target control from drifting (Sustaining) or
shapes decisions toward alignment (Informing). See [`07-control-roles-faircam.md`](./07-control-roles-faircam.md).

**Rightsizing** — Qualifying a control: weighing the Force ledger against the
Drag ledger to produce one of four verdicts — the step that cashes out "a strong
control can still be a bad control." See [`04-rightsizing.md`](./04-rightsizing.md).

**Signature** — A control's full identity: its Role, one or more Function tags,
and exactly one Disposition per objective path it touches (e.g. "Direct
Deny/Tax"). See [`01-framework.md`](./01-framework.md) §2.

**Sustaining control** — A control acting on *another control* — keeping it
reliable by catching and correcting its degradation. FAIR-CAM's Variance
Management Controls. See [`07-control-roles-faircam.md`](./07-control-roles-faircam.md).

**Tax** — The Disposition where a cost is paid but the population *stays on the
sanctioned path* — friction people absorb, not friction they escape. Not Distort.
See [`03-taxonomy-disposition.md`](./03-taxonomy-disposition.md).

**Tunability** — The Rightsizing deploy gate: can the control be scoped, staged,
or relaxed? No exit ramp, no deploy. See [`04-rightsizing.md`](./04-rightsizing.md).

**Undersized** — The Rightsizing verdict where Drag is paid but Force is
insufficient for the modeled threat — strengthen it or stop paying for it. See [`04-rightsizing.md`](./04-rightsizing.md).
