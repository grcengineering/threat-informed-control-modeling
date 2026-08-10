---
name: ticm-control-modeling
description: Author a reusable, threat-informed control model for a named security control using the TICM method — Role, Function (with falsification tests), Disposition, and a Rightsizing verdict. USE WHEN asked to author a control model, model a control, build a threat-informed control model, produce a TICM control model, or write a control effectiveness model for a control class (WAF, MFA, EDR, change-management gate, DLP, etc.). NOT FOR assessing a whole system or project's deployed controls (use ticm-assessment).
license: Unlicense
---

# TICM Control Modeling (Proactive)

Author one **reusable, portable control model** for a named control — the durable document another engineer deploys from without re-deriving the threat or the trade-off. This is TICM's proactive mode: you model a control *class* once (WAF, MFA, EDR, change-gate) as a classifier and tell both stories at once — what it catches (the adversary) and what it costs (the business).

The single source of truth is [`../../docs/01-framework.md`](../../docs/01-framework.md); it wins on every conflict. Fill [`../../templates/control-model-template.md`](../../templates/control-model-template.md) section by section. This skill is the imperative form of [`../../methodology/proactive-control-modeling.md`](../../methodology/proactive-control-modeling.md); browse [`../../examples/controls/`](../../examples/controls/) for worked models first.

## When to use / when not

**Use** when asked to model, characterize, or judge the effectiveness of a *single named control* and produce a reusable artifact. **Do not use** to assess a whole system's deployed controls against its threats — that's the reactive `ticm-assessment` skill. Never invent taxonomy: every Role, Function, Disposition, and verdict must match the spec exactly, spelled as written.

## Inputs to gather first

Get these from the requester before writing — ask if missing, do not guess:

1. **Control** — the real mechanism (not a policy wish), ideally with a Control ID.
2. **Environment** — where it's deployed, what it fronts, the team that runs it.
3. **Threats** — the adversary and technique(s) it mitigates, with ATT&CK coordinates where known.
4. **Objectives / critical paths** — the business flows it touches (revenue capture, deploy velocity, onboarding, support) and which are revenue-critical.

If the threats or objective paths are unknowable, say so — a model with no attack graph and no objective paths cannot be rightsized.

## Workflow

Work in order. Each step names the artifact and the **template section** it fills (`→ §N`); `(spec §N)` points into the framework doc. Copy these four house-palette styles verbatim into every diagram, as `style <nodeId> <spec>`:

```
Attacker node:        style <id> fill:#ff7676,stroke:#940000,stroke-width:2px,color:#000000
Risky / at-risk node: style <id> fill:#f9ebb9,stroke:#fb9400,stroke-width:2px,color:#000000
Protected asset node: style <id> fill:#9bb8ff,stroke:#0035b3,stroke-width:2px,color:#000000
Control node:         style <id> fill:#9ecea2,stroke:#015407,stroke-width:2px,color:#000000
```

1. **Assign the Role → Front Matter.** Decide what the control acts on: **Direct** (attack graph / asset), **Sustaining** (another control), or **Informing** (a decision). This sets the Function sub-taxonomy for step 3 — get it wrong and you measure against the wrong thing. (spec §3)
2. **State the threat and draw the threat model → §1–2.** Name adversary, target, technique(s). Diagram the attack path against the asset *independent of the control* — this is the attack graph Coverage is later measured against.
3. **Tag Function(s) with falsification tests → §4.** Direct controls use **Deny · Degrade · Detect · Deceive · Contain · Evict · Restore**; Sustaining/Informing use **Prevent · Identify · Correct** (variance / misalignment). Every tag must name a falsification test that must behave a specific way, or it's a lie. A sensor that also blocks is two tags. A backup/DR control is typically **Restore** here *and* **Enable** on the Disposition axis — keep them separate. For controls whose test cannot be adversary-emulated (administrative/procedural — background checks, segregation-of-duties, vendor clauses, most Informing controls), evidence Efficacy by the **alternate tier** (process audit, historical base-rate data, or design-review against the dependency graph); do not auto-score them zero for "never emulated." Note the Functions it does *not* carry. (spec §4)
4. **Enumerate objective paths and assign Dispositions → §6.** List objective flows first, then place the control on each it intersects and assign exactly one Disposition **per path, per named bearing population**: **Enable · Neutral · Tax · Distort · Block**. "It's a Tax" is noise; "Tax on the deploy-velocity path borne by the 40-person platform team, ~6 min/deploy × ~200 deploys/week" is a finding. Neutral is earned by enumeration; Distort is behavioral — did a route-around *pattern* emerge? (spec §5)
5. **Draw the control mechanism → §5.** The actors, systems, and steps that make the control work, in the §2 visual language so the two read side by side.
6. **Build the dependency schema → §7.** Split every precondition into **Enablement (EN) / Routing (RT) / Enforcement-boundary (EB)**, each row a stable ID, a verification method, and its drift risk. These IDs are load-bearing in steps 7–9.
7. **Draw the control-bypass threat model → §8.** The control is now the asset; each bypass exploits a specific dependency ID. **Every Distort from step 4 seeds a bypass row here** — the Coupling Law made concrete: a workaround is a legitimate flow that left the boundary, so friction converts into attack surface. (spec §7)
8. **Run Rightsizing and record the verdict → §9.** Weigh the **Force** ledger (Efficacy, Coverage, Bypass-resistance) against **Drag** (Friction, Distortion-pressure, Sustainment); render one verdict: **Rightsized · Oversized · Undersized · Miscast · Misfit**. Score Force against *the modeled threat the control was deployed to address*, not an unrelated threat it happens to touch (the Oversized/Undersized precedence rule). **Miscast** is the adversary-side kind error (wrong Function for the threat); **Misfit** is the organization-side counterpart — right Function and sufficient Force, but a **material, unmitigated** Distort or Block on an intersected objective path makes it net-negative. Apply both hard gates — no exit ramp (Tunability) → no deploy; a material, unmitigated Distort/Block on a critical path → the verdict is **Misfit** and it does not deploy as-is, however strong the Function. (spec §6)
9. **Write Designed / Implemented / Operating → §10.** Resolve each tier to dependency IDs. Operating requires adversary-emulation validation *and* a stated Distortion threshold — a control failing its Coupling-Law check is not operating, however green its config. (spec §8)
10. **Map frameworks and cite sources → §12–13.** SOC 2 / ISO 27001 / NIST / PCI / CIS as applicable, then references (ATT&CK/D3FEND coordinates, bypass catalog, FAIR-CAM Role source).

## Output contract

One completed control-model markdown file matching [`../../templates/control-model-template.md`](../../templates/control-model-template.md) — every section filled, both mermaid diagrams valid and using the house palette, and front matter carrying the Role, Function tag(s), per-path Disposition(s), and the Rightsizing verdict. It must read as reusable: another engineer could deploy from it cold.

## Self-check gate — pass every item before declaring done

- [ ] Role assigned first, and it dictates the Function set used.
- [ ] Every Function tag carries a falsification test naming an emulated attack — no bare labels.
- [ ] Every objective path has exactly one Disposition, per named bearing population; Neutral is earned by enumeration.
- [ ] Every Distort disposition has a matching bypass row in §8 (the Coupling-Law check).
- [ ] Exactly one Rightsizing verdict is recorded from the five (**Rightsized · Oversized · Undersized · Miscast · Misfit**), and both hard gates (Tunability; material+unmitigated Distort/Block on a critical path → Misfit veto) are explicitly checked.
- [ ] Each Assurance tier resolves to specific dependency IDs; Operating names emulation validation *and* a Distortion threshold.
- [ ] Both mermaid blocks parse and use the exact house palette (attacker / risky / asset / control).
- [ ] No framework mapping is presented as verified — the illustrative-unless-verified disclaimer is present.
