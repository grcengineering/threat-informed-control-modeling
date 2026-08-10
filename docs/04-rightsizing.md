# Rightsizing — Qualifying the Control

> Categorizing a control gives you its grid signature (Role, Function, Disposition).
> **Rightsizing** decides whether it's the *right* control, *right-sized* for this
> organization against these threats. This is where TICM cashes out its promise that
> a strong control can still be a bad control. Canonical definitions live in
> [../docs/01-framework.md](../docs/01-framework.md) §7; this file is the working rubric.

Rightsizing weighs two ledgers against each other, **per intersected objective path**:
a **Force** ledger that faces the adversary, and a **Drag** ledger that faces the
organization. Each dimension is anchored to something you can observe — not a feeling —
and scored in coarse bands. The output is a **verdict**, not a number, and by design
not an acronym.

## The Force ledger (adversary-facing)

Three dimensions. For Sustaining and Informing controls, read "attack paths" as
"the variance managed" or "the decisions supported" — the ledger is the same, the
target is what §3 of the framework assigns.

| Dimension | Observable anchor | Low (0) | Moderate (1) | High (2) |
|---|---|---|---|---|
| **Efficacy** | Does the claimed Function pass its §4 falsification test — adversary emulation for an Adversarial claim, else the Risk-Source-matched alternate-evidence tier below? | Fails its test, or no evidence of any kind | Passes for the headline technique / a limited-scope audit only | Passes across the technique family under emulation, or the full alternate-tier equivalent |
| **Coverage** | Fraction of the modeled attack paths (from the §2 Threat Model of the template) this control actually sits on | Touches a minority of enumerated paths | Touches roughly half | Sits on nearly every modeled path to the asset |
| **Bypass-resistance** | Cost of the adversary's cheapest known way around it (the §8 bypass model) | Trivial, documented bypass | Bypass needs real effort or tooling | No cheap bypass known; workarounds are costly and noisy |

Score each 0-2 and read the total (0-6) as a band: **weak Force** (0-2),
**moderate** (3-4), **strong** (5-6). Efficacy is a gate on the other two — a control
that *fails* its falsification test has zero real Coverage no matter how many paths it
nominally sits on.

**The alternate-evidence tier.** Emulation is the gold standard, not a precondition for a
nonzero score — it is simply the falsification evidence that applies when a control's
claimed Risk Source is **Adversarial** (framework [§5](../docs/01-framework.md); full
treatment in [08-risk-source.md](../docs/08-risk-source.md)). Plenty of real controls can't
be adversary-emulated — administrative and procedural ones (background checks,
segregation-of-duties, vendor security clauses, access reviews) and most Informing controls
have no attack to throw at them. That used to read as a gap patched over with a generic
"process audit" fallback. It isn't a gap once the Risk Source is named honestly: these
controls are rarely defending against an attacker at all. An access review's real Risk
Source is **Structural** — entitlements drifting from role as people change jobs and nobody
removes the old access. A data-classification policy's is mostly **Accidental** — people
mishandling data because expectations were never made clear. A documented asset inventory's
is also **Structural** — nobody knows what to protect once the map drifts from reality. A
business-continuity plan's is **Environmental** — fire, flood, or regional outage. Each
earns its Efficacy score from the falsification evidence *its own* Risk Source defines: an
**injected-error scenario** for Accidental (does the control catch a realistic honest
mistake?), an **injected-drift scenario** for Structural (does it catch state that has
silently diverged from its designed baseline?), or a **DR/continuity drill** for
Environmental (does it perform under simulated disaster?). None of these are auto-scored 0
for "never emulated" — emulation was never the applicable test for them. A control earns a
0 on Efficacy only when it *fails* its test — emulated or Risk-Source-matched alternate — or
offers no evidence of either kind, never merely because no packet was ever thrown at it.

## The Drag ledger (organization-facing)

Three dimensions, each read **per named objective path with a bearing population** —
never as one global label. "This control is friction" is noise; "6 min/deploy × 200
deploys/week on the 40-person platform team" is a finding.

| Dimension | Observable anchor | Low (0) | Moderate (1) | High (2) |
|---|---|---|---|---|
| **Friction** | Measured Tax magnitude on each objective path (time, latency, dollars) with the population still on the sanctioned path | Negligible overhead | Real, absorbed overhead | Heavy overhead people grudgingly pay |
| **Distortion-pressure** | Observed or confidently predicted circumvention rate — shadow IT, shared creds, standing exceptions, personal devices | No route-around behavior | Workarounds emerging or plausibly predicted | Route-around is the norm |
| **Sustainment** | Upkeep + triage burden + the control's own fragility as a single point of failure | Self-sustaining, low triage | Steady upkeep, some alert load | Constant tuning; brittle; a SPOF |

**Distortion-pressure is weighted above the other two.** Per the Coupling Law
([01-framework.md](../docs/01-framework.md) §8), circumvention is not just a UX cost —
it removes a legitimate flow from the enforcement boundary, which erodes the control's
own adversary-facing Coverage: that flow now runs outside this control's boundary,
unmanaged by it and unknown until the paths are re-enumerated (defense-in-depth may still
cover it). Friction converts into attack surface the model can't see. That is why every
Distort rating auto-writes an entry into the control-bypass threat model, and why a high
Distortion-pressure score can sink an otherwise strong control on its own — the **Misfit**
verdict below.

## The five verdicts

You are comparing Force against Drag on **every** objective path the control intersects.
The rule below is exhaustive and deterministic — every control lands on exactly one of five
verdicts. Two of them are **kind errors** no dial can fix: **Miscast** (wrong Function for
the threat — the *adversary* side) and **Misfit** (right Function, sufficient Force, but a
material Distort or Block disqualifies it — the *organization* side). The other three —
Rightsized, Oversized, Undersized — are questions of degree you tune.

```mermaid
flowchart TD
    start([Score Force vs Drag per objective path])
    q1{Right Function<br/>for the threat?}
    q4{Material, unmitigated<br/>Distort or Block on<br/>a critical path?}
    q5{Does clearing the Distort/Block<br/>restore sufficient Force —<br/>is the Force failure ENDOGENOUS<br/>to it, or independent?}
    q2{Force materially<br/>exceeds Drag on<br/>EVERY path?}
    q3{Which side is<br/>underpowered?}
    miscast[MISCAST — wrong Function, replace it]
    misfit[MISFIT — veto, retune or redesign]
    right[RIGHTSIZED — keep, plus Tunability gate]
    over[OVERSIZED — turn down / scope / stage]
    under[UNDERSIZED — strengthen or stop paying;<br/>log the Distort as a second, independent finding]
    start --> q1
    q1 -->|No| miscast
    q1 -->|Yes| q4
    q4 -->|Yes| q5
    q5 -->|Endogenous — the Distort IS<br/>the Force failure, or causes it| misfit
    q5 -->|Independent — Force is weak<br/>for an unrelated reason| under
    q4 -->|No| q2
    q2 -->|Yes| right
    q2 -->|No| q3
    q3 -->|Force exceeds the modeled threat| over
    q3 -->|Drag paid but Force too weak| under
    style miscast fill:#ff7676,stroke:#940000,stroke-width:2px,color:#000000
    style misfit fill:#ff7676,stroke:#940000,stroke-width:2px,color:#000000
    style over fill:#f9ebb9,stroke:#fb9400,stroke-width:2px,color:#000000
    style under fill:#f9ebb9,stroke:#fb9400,stroke-width:2px,color:#000000
    style right fill:#9ecea2,stroke:#015407,stroke-width:2px,color:#000000
```

**The precedence rule that makes this exhaustive.** The veto check (q4) runs
*before* the Force/Drag score, not only when Force turns out strong — the
original one-directional flowchart let a control with **weak** Force and a
material Distort skip the veto entirely and land on Undersized, which
contradicted Gate 2 below ("regardless of how strong the Function is"). The
fix is q5: when a material, unmitigated Distort/Block is present, ask whether
clearing it is *what restores* sufficient Force. Two shapes:

- **Endogenous** — the Distort/Block and the Force failure are the same
  event, or the Distort directly causes the Force failure (a rubber-stamped
  approval is simultaneously the Disposition failure *and* the reason the
  control's own Function claim is false; a shared-workaround account removes
  the population the Function was ever protecting). Clearing the Distort is
  what restores Force. → **Misfit**.
- **Independent** — Force is insufficient for a separate, unrelated reason
  (weak crypto, a stale ruleset, a config nobody strengthened), and the
  Distort is a second, independent problem, not the cause of the first. →
  **Undersized**, with the Distort logged as its own no-deploy finding
  alongside it — fixing Force alone does not clear the veto, and fixing the
  Distort alone does not fix the Force.

- **Rightsized** — Force materially exceeds Drag on every intersected path, there's an exit
  ramp, and no path carries a material, unmitigated Distort or Block. *Example:* SSO that
  both blocks credential-stuffing (Deny) and speeds onboarding (Enable) — strong Force,
  negative Drag.
- **Oversized** — Force exceeds the threat that actually applies; the Drag is unjustified.
  *Example:* hardware-token MFA forced on read-only interns whose access carries no
  loss-event weight. Turn it down, scope it, or stage it.
- **Undersized** — the Drag is paid but Force is insufficient for the modeled threat.
  *Example:* a WAF in monitor-only mode on an internet-facing checkout API — friction and
  cost incurred, near-zero Deny. Strengthen it or stop paying.
- **Miscast** — wrong **Function** for the threat entirely — the *adversary-side* kind
  error. No amount of tuning fixes it. *Example:* answering a data-exfiltration threat
  (which needs Contain) with more logging (Detect). Replace it.
- **Misfit** — right Function and *sufficient* Force against the modeled threat, but a
  **material, unmitigated** Distort or Block on an intersected objective path makes deploying
  it net-negative — the *organization-side* kind error, and the verdict the deploy veto
  produces. It is the matched counterpart to Miscast: Miscast is the wrong tool for the
  threat, Misfit is the right tool whose Drag disqualifies it. This is the verdict that names
  the thesis directly — *great against the attacker, bad for the business*. *Example:* a DLP
  proxy that genuinely stops the exfil technique (strong Deny) but drives every engineer onto
  personal devices to get work done. Retune it or redesign it; do not ship it as-is.

**Precedence rule — name the threat first.** Always score Force against **the modeled threat
the control was deployed to address**, not an unrelated threat it happens to also touch. This
resolves the Oversized/Undersized tie: a control that is strong against something nobody is
defending here, and weak against the thing actually in scope, is **Undersized** for the
modeled threat — not Oversized for the incidental one. Name the threat, then judge the fit.

## Two hard gates

These sit **outside** the Force score and can override any verdict.

1. **Tunability is the deploy gate.** If the control cannot be scoped, staged, or relaxed,
   it has no exit ramp. *No exit ramp, no deploy.* A control you can only run at 100% or 0%
   is a liability the day its threat model changes.
2. **A material, unmitigated Distort or Block on a critical path is a veto.**
   A Distort or Block that is both *material* (circumvention above a stated threshold on a
   path that matters) and *unmitigated* (no exit ramp **and** no Sustaining control catching
   and correcting it, per [01-framework.md](../docs/01-framework.md) §3) vetoes deployment
   **regardless of how strong the Function claim looked before the veto ran** — the flowchart
   checks this veto before scoring Force/Drag, not after (q4 runs before q2). Where that
   veto lands depends on q5's endogenous/independent split above: if the Distort/Block IS the
   Force failure or causes it, the verdict is **Misfit**; if Force is independently weak for
   an unrelated reason, the verdict is **Undersized** with the Distort logged as its own
   no-deploy finding — either way, this control does not deploy as-is. This is the rule that
   operationalizes "a control that mitigates threats but disrupts objectives is a bad
   control." A *managed* Distort — one with a sanctioned exception ramp and a Sustaining
   control behind it — is still a Distort you model (you still write its control-bypass
   entry), but it is not material/unmitigated, so it does not trigger this gate. The fix is never
   "ship it anyway"; it is retune until the Distort clears, or add the exit ramp / Sustaining
   control that downgrades it to managed.

## Why there is no acronym

DREAD is the cautionary tale. Its five letters — Damage, Reproducibility,
Exploitability, Affected users, Discoverability — are summed into an unweighted average,
and two traits are why it aged badly. The forced acronym is the first: squeezing every
assessment through the same five fixed letters bends it to fit the mnemonic instead of the
risk. The flat average is the second: it lets a catastrophic Damage score get washed out
by a low Discoverability score. Microsoft quietly retired it. TICM refuses both traps on
purpose: the dimensions are named for what they measure, Distortion-pressure is explicitly
weighted above its peers, and the output is a **verdict you have to reason to**, not a mean
you can hide behind.

## The deep tier — Control Net Value (optional)

Teams with the measurement maturity can deepen the same ledger into a quantitative
**Control Net Value**, a FAIR-CAM-style estimate:

```
CNV ≈ (ΔALE × coverage × reliability) − net-drag,   with calibrated intervals
```

where ΔALE is the reduction in annualized loss expectancy the control's Function buys,
scaled by its Coverage fraction and its Operating reliability, minus the dollarized Drag.
**CNV is the ceiling, not the entry bar.** TICM is deliberately designed so a team can
run the verdict bands on day one with observables a SOC already produces — emulation
results, alert-triage records, route-around telemetry. Reach for CNV when a decision is
expensive enough to justify calibrated intervals; don't let its absence stop you from
rendering a verdict.

## Worked rightsizing, end to end

**Control:** All service credentials must be requested through a manual ticket-based
approval workflow (24-48h SLA) before use.
**Signature:** Direct · Degrade (raises adversary cost of obtaining valid credentials at
T1078) · Distort on the deploy-velocity path.
**Risk Source:** Adversarial — left unlabeled in the signature per the framework's notation
convention (§5), but worth naming here: the Force ledger below is scored against an
intentional attacker obtaining a valid credential, not against (say) an engineer's honest
provisioning mistake — a different Risk Source that would need a different falsification
test. It's also why Efficacy has to clear an emulated-attack bar rather than the
alternate-evidence tier: Adversarial claims don't qualify for it.

```mermaid
flowchart LR
    at(Attacker)
    ticket(Approval-gate control)
    creds[(Service credentials)]
    devs[Dev team - deploy path]
    shadow[Hardcoded long-lived creds]
    at -->|1. steals a valid credential| creds
    ticket -->|2. Degrade: slows credential grant| at
    devs -->|3. route around 48h SLA| shadow
    shadow -->|4. new surface outside the enforcement boundary| at
    style at fill:#ff7676,stroke:#940000,stroke-width:2px,color:#000000
    style creds fill:#9bb8ff,stroke:#0035b3,stroke-width:2px,color:#000000
    style ticket fill:#9ecea2,stroke:#015407,stroke-width:2px,color:#000000
    style devs fill:#f9ebb9,stroke:#fb9400,stroke-width:2px,color:#000000
    style shadow fill:#f9ebb9,stroke:#fb9400,stroke-width:2px,color:#000000
```

**Force ledger.** Efficacy **1** — emulation confirms it slows *net-new* credential
grants but does nothing to already-issued ones. Coverage **1** — it sits on the
provisioning path only; theft of existing creds bypasses it entirely. Bypass-resistance
**0** — the §8 bypass model shows a trivially cheap workaround (hardcode a long-lived
credential once, never file another ticket). **Force = 2 of 6 (weak).**

**Drag ledger, on the deploy-velocity path (borne by ~60 engineers).** Friction **2** —
a 24-48h SLA against a several-times-daily deploy cadence. Distortion-pressure **2** —
route-around telemetry shows hardcoded long-lived credentials in repos, exactly the
Coupling-Law prediction. Sustainment **1** — a standing approval queue with real triage
load. **Drag = 5 of 6 (heavy).**

**q5, endogenous or independent?** Independent. The weak Coverage/Bypass-resistance score
(the gate never covered already-issued credentials and was always trivially bypassable)
would be identical even in a universe where nobody ever routed around it — the Force
failure predates and does not depend on the Distort. The Distort is a *second*, separate
consequence of the same badly-designed control, not the cause of the Force problem.

**Verdict: Undersized**, with the Distort logged as an independent no-deploy finding. Drag
materially exceeds Force on the deploy path — Force is 2 of 6 against the modeled
credential-theft threat, with a trivially cheap bypass — so it fails the Force-exceeds-Drag
test, scored (per the precedence rule) against the threat this gate was actually deployed
for. Per q5, that reads **Undersized**, not **Misfit**: Misfit is reserved for the
*endogenous* case, where clearing the Distort is what restores Force, and here it wouldn't —
strengthen the Force alone (cover existing credentials, close the hardcoding bypass) and the
route-around incentive is untouched, so the Distort veto still fires; only a redesign that
removes *both* — short-lived auto-issued credentials, which close the bypass and remove the
reason to route around — reaches Rightsized. The Coupling Law is why leaving it as Undersized
is worse than a wash: the circumvention it provokes (long-lived hardcoded creds) is a
*stronger* version of the very threat it was meant to Degrade — friction converted into
attack surface the model can't see.
**Action:** replace the manual gate with short-lived, auto-issued credentials (an
Enable-disposition redesign that removes the route-around incentive and lifts Force at the
same time), and keep the approval workflow only for the rare high-privilege grant where the
48h Tax is genuinely absorbed rather than escaped.

---

*See [../docs/01-framework.md](../docs/01-framework.md) for the full model,
[../docs/03-taxonomy-disposition.md](../docs/03-taxonomy-disposition.md) for
Objective-Path Analysis, [../docs/08-risk-source.md](../docs/08-risk-source.md) for the
Risk Source axis behind the alternate-evidence tier, and [../templates/](../templates/) for
the control document template this rubric plugs into.*
