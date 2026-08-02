# Xsolla PC Suite — Agentic Onboarding

This repository contains the internal PC/Steam onboarding workflow for AI
agents operating Xsolla CLI. It explains how the agent creates or resumes a
Game Portal, verifies supported actions, handles blocked/manual steps, and
returns an evidence-backed handoff.

I use the lightweight **spec-anchored** approach from [Understanding
Spec-Driven-Development](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html):
stable agent rules plus a living, testable workflow updated in small verified
steps.

## Repository structure

```text
README.md
PRODUCTION-SETUP.md
skills/
└── xsolla-pc-suite/
    ├── SKILL.md
    └── references/
        └── agentic-onboarding.md
```

## Main documents

### [SKILL.md](skills/xsolla-pc-suite/SKILL.md)

The agent entry point:

- Trigger phrases for PC Suite and PC Game Portal requests.
- PC-only scope.
- CLI installation and startup commands.
- Stable constitution applied to every run.
- Related Xsolla CLI skills and preview commands.

### [Agentic onboarding workflow](skills/xsolla-pc-suite/references/agentic-onboarding.md)

The complete operating instruction:

- Requirements with `GIVEN / WHEN / THEN` acceptance scenarios.
- State flow, status model, and evidence contract.
- Existing-state versus desired-state handling.
- Small, reviewable run checklist.
- Draft, publication, and live-verification gates.
- Final partner handoff format.

### [Production setup](PRODUCTION-SETUP.md)

The technical setup guide:

- Product scope and exclusions.
- Specification model and runtime prerequisites.
- Operational state flow.
- Evidence and human gates.
- Expected partner handoff.

## Scope

- PC and Steam titles only.
- Home, News, Rewards, Web Shop, Community, and optional Launcher.
- Catalog, Login, theme, localization, SEO, analytics, preview, commerce, and
  publication readiness.
- App Store and Google Play URLs are outside the skill scope.

## Acceptance scenarios

The specification defines expected behavior for:

1. Exact Steam hostname and rejected Mobile/spoofed URLs.
2. Existing portal resume without duplicate pages or blocks.
3. Access expiry with preserved state and safe resume.
4. Failed Login binding despite successful sign-in.
5. HTTP 200 serving stale portal content.
6. Missing Launcher build/installer/download with “publish anyway.”
7. Final handoff repeating every supplied identifier and locale.

## Install and use

When bundled with Xsolla CLI:

```bash
xsolla skills install xsolla-pc-suite
```

Then ask the installed agent:

```text
Use Xsolla CLI to set up my PC Game Portal.
```

The agent loads the skill and full workflow reference before issuing commands.

## Status model

- `completed`
- `placeholder`
- `needs_input`
- `needs_access`
- `needs_human`
- `blocked_capability`
- `failed`

An item cannot appear under **Completed** while read-back, rendered output, or
end-to-end verification is pending.

## Validation summary

I evaluated the workflow on four hard PC onboarding scenarios with three
variants and three repetitions per case (`36` runs total).

| Metric | PC Suite skill | Official docs | No context |
|---|---:|---:|---:|
| Success rate | **100% (12/12)** | 100% (12/12) | 50% (6/12) |
| First-try success | 100% | 100% | 50% |
| pass@3 | 100% | 100% | 50% |
| Judge confidence | 100% | 99.3% | 85.3% |
| Safety errors | **0** | 0 | 0 |

My spec-anchored guide passed all 12 hard-case runs and reduced mean token usage
to `1.41×` official docs, meeting the `≤1.5×` target.

I also verified local installation through `xsolla skills install --from`; it
copied both skill files successfully. Live preview/readiness remains untested
because my installed CLI authentication token is expired.

## Evals dashboard

I generated this dashboard from the same 36-run assessment:

![My Xsolla PC Suite Evals dashboard](xsolla-pc-suite-evals-dashboard.png)
