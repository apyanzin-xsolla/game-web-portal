# Xsolla Game Web Portal — Agentic Onboarding

## What Agentic Onboarding is

Onboarding done by an AI agent instead of a person clicking through a dashboard.
You give the agent a Steam store URL and your identifiers. It drives Xsolla CLI to
create or resume your Game Web Portal, checks the result of every change it makes,
and hands back a report you can audit line by line.

What makes it trustworthy is a single rule: **the agent reports only what it
verified.** It never invents an ID, a command, a price, or a completion. Anything it
could not finish is named explicitly, with the reason and whose job it is to fix.
Honest partial completion is the expected outcome; a clean-looking report that
wasn't verified is a failure.

The workflow is **spec-anchored**, following [Understanding
Spec-Driven-Development](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html):
stable agent rules plus a living, testable workflow updated in small verified
steps.

## How to use it

Install the skill from inside either folder, authenticate, then ask:

```bash
xsolla skills install --from . game-web-portal
xsolla auth login
```

```text
Use Xsolla CLI to set up my PC Game Portal.
```

The agent asks for `merchant_id`, `project_id`, `domain`, `game_name`, `store_url`,
`primary_locale`, and whether an existing portal should be updated or replaced — all
in one question — then works through this flow:

```text
Intake → Preflight → Discover → Draft → Verify → Human review →
Publish → Live verification → Handoff
```

It stops and asks whenever access needs renewing, an existing entity is ambiguous, a
change would be destructive, the CLI lacks a needed capability, or publication must
happen in Publisher Account. Progress is preserved, so a resumed run continues rather
than rebuilding.

## Repository structure

The same workflow is packaged twice, once for each place the skill can be installed
from. Both folders are self-contained, and both drive the identical onboarding
specification — pick whichever matches how you get your Xsolla skills.

- **[CLI](CLI/README.md)** — for the Xsolla CLI's built-in skill set.
- **[AI-Kit](AI-Kit/README.md)** — for the Xsolla AI-Kit skill collection.

```text
README.md
PRODUCTION-SETUP.md
game-web-portal-evals-dashboard.png
CLI/
├── README.md
└── skills/game-web-portal/
    ├── SKILL.md
    ├── VERSION
    ├── CHANGELOG.md
    └── references/agentic-onboarding.md
AI-Kit/
├── README.md
└── skills/game-web-portal/
    ├── SKILL.md
    └── references/agentic-onboarding.md
```

## Main documents

### [SKILL.md](CLI/skills/game-web-portal/SKILL.md)

The agent entry point:

- Trigger phrases for Game Web Portal and PC Game Portal requests.
- PC-only scope.
- CLI installation and startup commands.
- Stable constitution applied to every run.
- Related Xsolla CLI skills and preview commands.

The [AI-Kit variant](AI-Kit/skills/game-web-portal/SKILL.md) describes the same
workflow.

### [Agentic onboarding workflow](CLI/skills/game-web-portal/references/agentic-onboarding.md)

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

| Metric | Game Web Portal skill | Official docs | No context |
|---|---:|---:|---:|
| Success rate | **100% (12/12)** | 100% (12/12) | 50% (6/12) |
| First-try success | 100% | 100% | 50% |
| pass@3 | 100% | 100% | 50% |
| Judge confidence | 100% | 99.3% | 85.3% |
| Safety errors | **0** | 0 | 0 |

My spec-anchored guide passed all 12 hard-case runs and reduced mean token usage
to `1.41×` official docs, meeting the `≤1.5×` target.

I also verified local skill installation and ran a live smoke check against an
existing portal:

- Website discovery and structure read: passed.
- Preview enablement and preview-link generation: passed.
- Public preview URL: HTTP 403.
- `verify-website`: HTTP 400 because the CLI request omits required
  `draftPagesIds`.

These are CLI integration gaps, not onboarding-spec failures.

## Evals dashboard

I generated this dashboard from the same 36-run assessment:

![My Xsolla Game Web Portal Evals dashboard](game-web-portal-evals-dashboard.png)
