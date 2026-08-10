# Prior Art — What TICM Borrows and What It Adds

TICM is a synthesis, and the honest thing to do is show the seams. Almost every
axis in the framework has a parent somewhere in the security literature — and even
the parts usually called "new," the **Disposition** axis and the **Coupling
Law**, have behavioral parents in the usable-security tradition, credited below.
What has *no* parent is narrower and more defensible: no prior **control-modeling
method** made a control's effect on organizational objectives a first-class,
per-control, generative part of the model — and that is the part worth defending.
This document goes framework by framework: a fair one-sentence summary, what
TICM **borrows** (be generous — these are debts, not rivals), and what TICM
**adds** that the parent does not have.

If you read only one thing: the **Role** axis is FAIR-CAM's, adopted and
credited. The **Function** axis is synthesized from the kill-chain and D3FEND.
The **assurance triad** is SOC 2's. The genuinely new parts are the
**Disposition** axis, the **Distort** category, the **Coupling Law**, and the
**Rightsizing verdict** (especially the matched pair **Miscast** and **Misfit**).
Everything below is the long version of that sentence.

## STRIDE / LINDDUN

**What it is.** STRIDE (Microsoft) enumerates threat categories against a
data-flow diagram — Spoofing, Tampering, Repudiation, Information disclosure,
Denial of service, Elevation of privilege; LINDDUN is its privacy-focused
sibling (Linkability, Identifiability, Non-repudiation, Detectability,
Disclosure, Unawareness, Non-compliance).

**What TICM borrows.** The data-flow-diagram discipline and the trust-boundary
convention come straight from STRIDE — TICM's §2 Threat Model diagram *is* a
STRIDE-style DFD, and the control-bypass threat model (§8) reuses the same
diagramming grammar with the control itself as the asset under attack. TICM also
inherits STRIDE's core instinct that you enumerate before you assert.

**What TICM adds.** STRIDE models the *attacker's* story and stops. It has no
representation of what a control does back to the adversary (the Function axis),
what it costs the business (the Disposition axis), or whether it's the right
control (Rightsizing). STRIDE finds threats; TICM models the controls that answer
them, on both the false-negative and false-positive sides of the same boundary.

## MITRE D3FEND

**What it is.** D3FEND is a knowledge graph of *defensive* techniques —
Harden, Detect, Isolate, Deceive, Evict, Restore, Model — cross-referenced to the
attacker techniques (ATT&CK) they counter.

**What TICM borrows.** A great deal, openly. TICM's seven Direct **Functions**
(Deny · Degrade · Detect · Deceive · Contain · Evict · Restore) are synthesized
from D3FEND's tactic set fused with the kill-chain Courses-of-Action (below) —
**Restore** in particular is D3FEND's own tactic, adopted for the loss-magnitude
tail the kill-chain verbs don't reach. The
house control template's function tags are explicitly *adapted from D3FEND's
tactic set for interoperability*, and TICM's "coordinates, not categories" rule —
that a control's ATT&CK/D3FEND technique IDs are coordinates the Function is
*applied at*, not extra Functions — exists precisely so TICM crosswalks to D3FEND
instead of forking it.

**What TICM adds.** D3FEND is a *catalog* of technique types; it is not a
per-control model, it carries no falsification tests, and it says nothing about
organizational cost. TICM binds each Function to a **falsification test** (a
detection nobody triages fails its Detect test — it's a log, not a control),
adds the Disposition axis D3FEND has no vocabulary for, and asks the Rightsizing
question D3FEND never poses: is this the right technique, sized correctly, for
*this* threat?

## Lockheed-Martin Kill-Chain Courses-of-Action

**What it is.** The Cyber Kill Chain's CoA matrix pairs each intrusion phase with
six defender actions — Detect, Deny, Disrupt, Degrade, Deceive, Destroy.

**What TICM borrows.** The verb set. TICM's Function axis is a deliberate
synthesis of these CoA verbs with D3FEND's tactics — Deny, Degrade, Detect, and
Deceive map almost directly; TICM renames Disrupt/Destroy toward Contain and
Evict to sharpen the post-compromise boundary (Contain acts on blast radius,
Evict removes occupancy). TICM does not claim this verb set as novel and says so
in `docs/02-taxonomy-function.md`.

**What TICM adds.** The kill chain is a linear staging model of an *intrusion*.
It has no notion of a control acting on *another control* (Sustaining) or on a
*decision* (Informing), no organizational-effect axis, and no per-control
qualification. TICM keeps the verbs as coordinates and builds the rest of the
model around them.

## FAIR-CAM (FAIR Controls Analytics Model)

**What it is.** FAIR-CAM (FAIR Institute, V1.0, January 2025) is a measurement
model that explains *how much* controls reduce risk, organizing every control
into three functional domains — Loss Event Controls, Variance Management Controls,
and Decision Support Controls — and quantifying their effect in risk units.

**What TICM borrows.** The **entire Role axis**, adopted wholesale and credited.
TICM's Direct / Sustaining / Informing Roles *are* FAIR-CAM's Loss-Event /
Variance-Management / Decision-Support domains. The Prevent/Identify/Correct
sub-triads for Sustaining and Informing controls are FAIR-CAM's. The optional
quantitative deep tier — Control Net Value (ΔALE × coverage × reliability −
net-drag) — is FAIR-CAM-style. This is the largest single debt in TICM and it is
front-and-center in `docs/07-control-roles-faircam.md`.

**What TICM adds.** FAIR-CAM answers *"how much risk does this control reduce, in
units?"* — a measurement model, and a demanding one. TICM answers *"what kind of
control is this, against which threats, at what cost to the business, and is it
the right one?"* — a modeling-and-design method that a SOC can run on day one with
observables it already produces. TICM fits FAIR-CAM's control-type structure onto
a threat-informed **attack-graph** front end (FAIR-CAM is threat-agnostic about
which specific attack paths a control sits on), adds the Disposition axis and the
Coupling Law (recognizing Distortion as a *variance event* that FAIR-CAM's
Variance-Management controls exist to catch), and produces a categorical
Rightsizing **verdict** rather than a number. They compose; they do not compete —
FAIR-CAM gains a threat-anchored, objective-aware design front-end, and TICM gets
its quantitative ceiling.

## NIST SP 800-30

**What it is.** NIST Special Publication 800-30 Revision 1, *Guide for
Conducting Risk Assessments* (September 2012), is the federal risk-assessment
methodology. Its Appendix D catalogs threat sources into four categories —
adversarial, accidental, structural, and environmental — as one classification
step inside a broader, organization-wide risk-assessment process.

**What TICM borrows.** The **Risk Source taxonomy**, adopted and credited:
Adversarial / Accidental / Structural / Environmental
([`01-framework.md`](./01-framework.md) §5). This traces to NIST SP 800-30
Rev. 1 (<https://csrc.nist.gov/pubs/sp/800/30/r1/final>), corroborated across
independent secondary sources describing its Appendix D taxonomy — the primary
PDF could not be directly rendered this session, which is disclosed rather
than papered over. **One honest extension, not wholesale:** NIST scopes
Structural to *technical*-system failure (hardware aging, resource
depletion); TICM deliberately broadens it to organizational systems
drifting from their designed state — see [`08-risk-source.md`](./08-risk-source.md)
for the full statement of the gap. The other three categories, and the
overall four-way split, are unchanged from NIST.

**What TICM adds.** SP 800-30 uses the four-category taxonomy as a single
classification step inside a linear, organization-wide risk-assessment
process — identify threat sources, identify threat events, assess likelihood,
assess impact, determine risk. It never attaches the taxonomy to an individual
control, and it has no notion of Function, Role, or Disposition. TICM lifts
the taxonomy out of that process and makes it a **first-class, per-control
axis**, crossed with Function, Role, and Disposition on every control — not a
one-time assessment step for the organization as a whole. That's the
difference between "an access review defends against entitlement drift"
sitting in a risk-assessment report and being a queryable field on the
control's model: `Sustaining · Identify(Structural) · Neutral`.

## Attack-Defense Trees

**What it is.** A formal method extending attack trees with interleaved defense
nodes, so an attacker's AND/OR goal decomposition and the defender's
countermeasures are modeled — and often cost- or probability-weighted — in one
tree.

**What TICM borrows.** The commitment to modeling attack and defense *together in
one structure* rather than in separate documents. TICM's insistence that you
enumerate attack paths (framework §7 Coverage) alongside the control that sits on them
shares this spirit, and TICM's Force ledger (Efficacy, Coverage,
Bypass-resistance) is comfortable consuming the kind of cost/probability
weighting ADTrees formalize.

**What TICM adds.** Attack-Defense Trees model a countermeasure's effect on the
*attacker* and, in cost-annotated variants, on the *defender's budget* — but they
have no concept of a control's effect on **organizational objectives**, no
Distort/circumvention dynamics, and no control-of-control (Sustaining) layer. The
Coupling Law — that organization-facing friction silently decays adversary-facing
coverage — is invisible to an ADTree, which treats a deployed defense as simply
present. TICM makes that decay the center of the model.

## Control Catalogs (SOC 2 / NIST CSF / CIS / ISO 27001)

**What it is.** Prescriptive libraries asserting *which* controls an organization
should have — SOC 2 Trust Services Criteria, NIST CSF Functions/Categories, CIS
Critical Security Controls, ISO/IEC 27001 Annex A.

**What TICM borrows.** SOC 2's **assurance triad** — Designed / Implemented /
Operating effectively — is adopted directly and openly as TICM's Assurance spine
(§9); TICM only sharpens "Operating" to demand adversary-emulation validation and
a Coupling-Law (no-Distortion) check. TICM also uses these catalogs the way they
are good: as the **framework-mapping** target (template §12), so a TICM control
model crosswalks to the compliance IDs an auditor expects.

> **Illustrative-mapping disclaimer.** Any SOC 2 / ISO 27001 / NIST / CIS / PCI
> control IDs cited in a TICM control model are **illustrative** and must be
> verified against the current published framework text before use in an audit or
> attestation; framework numbering and wording change between revisions.

**What TICM adds.** Catalogs assert a control *should exist* and stop there — they
tell neither the false-negative (bypass) story nor the false-positive (business
harm) story. They have no Function taxonomy, no Disposition axis, no Rightsizing,
and no per-control bypass threat model. A catalog says "you need MFA"; TICM says
"this MFA deployment is a **Direct Deny/Tax** control, Rightsized on the workforce
path but Distort on the contractor path, and here are its three bypass techniques
and the Sustaining control that catches it drifting to SMS fallback."

## Usable security & security economics

**What it is.** A research tradition — largely outside the control-modeling world
— that studies what security *does to the people subject to it*. Beautement, Sasse
& Wonham's **compliance budget** (2008) models each user as holding a finite
tolerance for security friction that depletes with every demand until compliance
stops. Herley's **rational rejection** economics (2009) shows users often
*correctly* decline security advice whose cost to them exceeds its expected
benefit. Kirlappos, Parkin & Sasse's **shadow security** (2014) documents the
constructive workarounds employees build when the sanctioned path is unworkable —
not malice, but an organization routing itself around its own unusable controls.
NIST's **security-fatigue** work (Stanton et al., 2016) puts empirical weight
under the same effect: decision overload drives risky shortcuts.

**What TICM borrows.** This is the honest one. TICM's **Distort** disposition *is*
shadow security, and the behavioral half of the **Coupling Law** — friction
provokes workarounds, workarounds leave the sanctioned path — is exactly what this
literature has described for over fifteen years. TICM did not discover that people
route around costly controls; the compliance budget did. The Distortion-pressure
signal in the Drag ledger (§7) is a compliance-budget reading by another name.
This is the largest single debt on the *organization-facing* side of TICM, the way
FAIR-CAM is the largest on the control-type side.

**What TICM adds.** The usable-security tradition studies the phenomenon
*descriptively and at population scale* — it explains why workarounds happen and
measures how common they are. What it does not do is make circumvention a
**modeled, per-control, generative** element of a control model. TICM *categorizes*
it (a distinct Disposition kind, not a severity score), *couples* it to the
control's own adversary-facing Coverage (the Coupling Law: a circumvented flow
leaves the enforcement boundary, going invisible to the control where the control
carries a Detect function, and dropping out of the population that control was
measured against — so its real Coverage falls below what the model claims),
*auto-generates* a control-bypass threat-model entry from every Distort rating, and
*hands* the resulting drift to the Sustaining / Variance-Management controls (§3)
whose job is to catch it. Shadow security tells you workarounds exist; TICM binds
that fact to a specific control, turns it into attack surface you can enumerate,
and routes it to the control that manages it. That move — from *described
phenomenon* to *load-bearing model element* — is the defensible novelty, and it is
deliberately narrower than "no one saw this before."

On the enablement side of the ledger, **SABSA** earns a one-line debt: its
business-attribute profiling tied security architecture to what the business
values years before TICM, and that instinct sits behind the **Enable** end of the
Disposition axis — SABSA works top-down from business attributes, TICM per-control
from the enforcement boundary, but both refuse to judge a control against the
attacker alone.

## Summary Table

| Framework | Models threats? | Models control **Function**? | Models **org-objective** effect? | Models **control-of-control** types? | Is a **per-control** model? |
|---|---|---|---|---|---|
| **STRIDE / LINDDUN** | Yes | No | No | No | No |
| **MITRE D3FEND** | Via ATT&CK crosswalk | Yes (technique catalog) | No | No | No (catalog) |
| **Kill-chain CoA** | Yes (staging) | Yes (verb set) | No | No | No |
| **FAIR-CAM** | Threat-agnostic | Yes (by domain) | Partial (drag as cost) | **Yes** (Variance/Decision) | Measurement, not design |
| **NIST SP 800-30** | Yes (threat-source taxonomy) | No | No | No | No (assessment step) |
| **Attack-Defense Trees** | Yes | Yes (countermeasures) | No (defender budget only) | No | Per-goal, not per-control |
| **Control catalogs (SOC 2/CSF/CIS/ISO)** | No | No | No | Partial (governance categories) | No (assertion) |
| **Usable security / econ (Sasse, Herley; SABSA)** | No (studies users) | No | **Descriptive** (population-scale) | No | No (phenomenon-level) |
| **TICM** | Yes (attack-graph) | **Yes** (7 Functions + triads) | **Yes** (Disposition axis) | **Yes** (Sustaining/Informing Roles) | **Yes** |

*The table is a positioning aid, not a scorecard — every "No" above marks a design
boundary the parent framework drew on purpose, and TICM is standing on all of
them at once.*

## The honest bottom line

TICM contributes four things and borrows the rest. The four: the **Disposition
axis** as a first-class categorical taxonomy of a control's effect on
organizational objectives; the **Distort** category and its elevation from a
*known* usable-security phenomenon (shadow security, the compliance budget) to a
modeled, per-control, generative property; the **Coupling Law** linking
organizational friction to adversary-facing coverage decay (and naming it a
variance event that Sustaining controls exist to catch); and the **Rightsizing
verdict** — most sharply the matched pair **Miscast** (wrong Function for the
threat — an adversary-side kind error) and **Misfit** (right Function and
sufficient Force, but a material, unmitigated Distort disqualifies it — the
organization-side kind error), the finding that a control can pass every efficacy
test and still be the wrong control. Everything else — the DFDs, the defensive
verb set, the three control Roles, the four Risk Sources, the
Prevent/Identify/Correct triads, the Designed/Implemented/Operating spine — is
credited to STRIDE, D3FEND, the kill chain, FAIR-CAM, NIST SP 800-30, and SOC 2
respectively; the behavioral observation behind Distort is credited to the
usable-security tradition (Sasse, Herley, and kin). The
defensible novelty is narrow and precise: **no prior control-modeling method made
a control's organizational objective-effect a first-class, per-control, generative
part of the model.** Naming the debts is the point — a control-modeling method
that hid its borrowings would fail its own honesty test.

## References

- Microsoft STRIDE; LINDDUN privacy threat modeling (linddun.org).
- MITRE D3FEND (d3fend.mitre.org); MITRE ATT&CK (attack.mitre.org).
- Lockheed Martin, *Cyber Kill Chain* and Courses-of-Action matrix.
- FAIR Institute, *FAIR Controls Analytics Model (FAIR-CAM) V1.0*, January 2025.
- NIST, *Special Publication 800-30 Revision 1: Guide for Conducting Risk
  Assessments* (September 2012) — <https://csrc.nist.gov/pubs/sp/800/30/r1/final>.
- Kordy et al., *Attack-Defense Trees* (foundational ADTree literature).
- AICPA SOC 2 Trust Services Criteria; NIST Cybersecurity Framework; CIS Critical
  Security Controls v8; ISO/IEC 27001 Annex A.
- Beautement, Sasse & Wonham, *The Compliance Budget* (NSPW 2008); Herley, *So
  Long, and No Thanks for the Externalities* (NSPW 2009); Kirlappos, Parkin &
  Sasse, *Learning from "Shadow Security"* (USEC 2014); Stanton, Theofanos,
  Prettyman & Furman, *Security Fatigue* (NIST authors; IT Professional, 2016).
- Sherwood, Clark & Lynas, *SABSA — Enterprise Security Architecture: A
  Business-Driven Approach*.
- Companion documents: [`01-framework.md`](01-framework.md),
  [`02-taxonomy-function.md`](02-taxonomy-function.md),
  [`03-taxonomy-disposition.md`](03-taxonomy-disposition.md),
  [`04-rightsizing.md`](04-rightsizing.md),
  [`05-assurance-spine.md`](05-assurance-spine.md),
  [`07-control-roles-faircam.md`](07-control-roles-faircam.md).
