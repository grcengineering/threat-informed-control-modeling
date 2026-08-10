# Contributing to TICM

TICM only becomes useful if it survives contact with real controls. The framework spec ([`docs/01-framework.md`](docs/01-framework.md)) is one person's synthesis until a hundred practitioners run it against their own WAFs, MFA rollouts, change-management gates, and threat-intel feeds and report where it bends. **The goal of this repo is to turn TICM into a shared, living practice** — a growing library of worked control models and a framework that gets sharper every time someone models a control it didn't quite fit. If you run TICM on a real control and something breaks, that report is the single most valuable thing you can send us.

This is a [grcengineering](https://github.com/grcengineering) project, and it inherits the org's open, build-in-public spirit. You don't need permission to start. Fork it, model a control, open a PR.

## Two kinds of contribution

**1. A new proactive control model** — the bread and butter. You model a control *class* once (say, an EDR agent, an S3 bucket policy, a code-review gate) into a portable document under [`examples/controls/`](examples/controls/), so a hundred deployments don't each re-derive it. This is where the library grows.

**2. Improvements to the framework, methodology, or skills** — a sharper falsification test, a Disposition boundary case the taxonomy doesn't handle cleanly, a missing crosswalk, a fix to a methodology step or a `SKILL.md`. These change how *everyone* models, so they get discussed before they get merged (see below).

## How to propose a control model

1. Copy [`templates/control-model-template.md`](templates/control-model-template.md) into `examples/controls/` under its new filename.
2. Work through [`methodology/proactive-control-modeling.md`](methodology/proactive-control-modeling.md) — its ten steps map one-to-one onto the template sections. Assign the **Role** first (Direct / Sustaining / Informing); it sets the meaning of every axis downstream. Get it wrong and you measure the control against the wrong thing.
3. Open a PR. Describe the control in one line and name anything you were unsure about — an ambiguous Disposition, a Function tag you nearly cut. Uncertainty is signal, not weakness; it's often where the framework needs work.

### Naming convention

Control IDs follow `DOMAIN.SUBDOMAIN.NUMBER`, mirroring the `community-security-controls` pattern — for example `IAM.MFA.1` for MFA on the identity provider, or `NET.WAF.1` for a WAF protecting a public web app. Pick the broadest domain that fits, keep subdomains short, and let the number disambiguate variants within a class.

**The filename is the control ID in dotted lowercase, with the number zero-padded to two digits** — so `IAM.MFA.1` lives in `iam.mfa.01.md` and `NET.WAF.1` in `net.waf.01.md`, matching the shipped examples and the `community-security-controls` sibling repo. Do *not* kebab-case the title into the filename. The in-file Control ID keeps its bare, unpadded number (`IAM.MFA.1`, never `IAM.MFA.01`); only the filename pads. The two-digit pad is what keeps a class's variants sorting in order once it grows past nine (`iam.mfa.09.md` before `iam.mfa.10.md`).

## The quality bar for a control-model PR

A control model is not a paragraph asserting that MFA is good. Before it merges, it must clear all of these:

- **Every mermaid block is valid.** It renders. Node IDs are defined before they're referenced, the flowchart header is well-formed, and diagrams use the house palette (attacker red, at-risk amber, protected asset blue, control green) — match the conventions in the template.
- **Every Function tag carries its falsification test.** A Function you can't falsify is a claim, not a finding. For a Direct control, each of Deny / Degrade / Detect / Deceive / Contain / Evict / Restore you tag needs the emulated attack (or, for Restore, the recovery drill under emulated destruction) that would prove it false (per [`docs/02-taxonomy-function.md`](docs/02-taxonomy-function.md)). For Sustaining and Informing controls, the same for the Prevent / Identify / Correct triad.
- **A Disposition per objective path.** Not one global label — one of Enable / Neutral / Tax / Distort / Block *per named objective path and bearing population*, earned by Objective-Path Analysis, never by not looking. Any **Distort** rating must write its entry into the control-bypass threat model (the Coupling Law is not optional).
- **A Rightsizing verdict.** Weigh the Force ledger against the Drag ledger and land on one of Rightsized / Oversized / Undersized / Miscast / Misfit, with the two hard gates checked — Tunability (no exit ramp, no deploy) and the material, unmitigated Distort/Block-on-a-critical-path veto, which forces the **Misfit** verdict however strong the Function.
- **The illustrative-mapping disclaimer.** Any framework crosswalk (SOC 2, ISO 27001, NIST, PCI, CIS…) must carry the one-line note that the IDs are illustrative and must be verified against current framework text before use. IDs and wording change between revisions.
- **Honest prior art.** If your model overlaps an existing one — a published control catalog, D3FEND, another repo entry — say so and say what yours adds. TICM is a synthesis that's honest about its seams; contributions hold the same standard.

## Proposing framework changes

Changes to the spec, the taxonomies, the methodology, or the skills start as an **issue**, not a PR. Open one describing the control that didn't fit or the case the taxonomy mishandles, and let it get discussed first. The framework is the source of truth every control model is written against, so it moves deliberately. Once there's rough agreement in the issue, the PR is the easy part.

## Code of conduct and licensing

Be a good colleague. This project follows the grcengineering organization's Code of Conduct (Contributor Covenant); by participating you agree to uphold it. Report unacceptable behavior to the org maintainers.

Everything here is released into the public domain under the [Unlicense](LICENSE), matching the rest of the repo. **By contributing, you dedicate your contribution to the public domain under the same terms** — no copyright strings attached, so anyone can copy, adapt, and build on TICM for any purpose. That's the point: a shared practice belongs to everyone who uses it.
