# Xsolla Game Web Portal — Agentic Onboarding

## What Agentic Onboarding is

Instead of asking users to configure everything manually in a dashboard, onboarding is handled by an AI agent.

The user provides a Steam Store URL and the required identifiers. From there, the agent uses the Xsolla CLI to create a new Game Web Portal or resume an existing setup. At every step, it validates the outcome before proceeding and produces a detailed, auditable report of everything it did.

### Designed for trust
The core principle is simple: the agent reports only what it has verified.

It does not guess or fabricate IDs, commands, prices, configuration states, or successful completions. If a task cannot be completed, the report clearly states:

- What was not completed

- Why it could not be completed

- Who needs to take the next action

Partial completion is acceptable—and often expected. A transparent report that accurately reflects the current state is more valuable than a polished success report built on unverified assumptions.

The workflow is **spec-anchored**, following [Understanding
Spec-Driven-Development](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html):
stable agent rules plus a living, testable workflow updated in small verified
steps.

## Verified implementation

`CLI` means the capability was executed through Xsolla CLI. `AI Kit` means its
skill was used as the operating instruction. `Partial` means direct REST or a
manual verification step was also required.

| Use case checked | What we achieved | API method used | CLI | AI Kit |
|---|---|---|:---:|:---:|
| Resume the existing portal without duplicates | Home and native block order configured | `GET /landings`; `GET /landing/{domain}/structure`; `PATCH /ui/{landingId}/batch` | Done | Done |
| Apply a consistent CS2 theme and imagery | Matching colors, buttons, and 49 native image slots | Multipart asset upload; `PATCH /ui/{landingId}/batch` | Done | Done |
| Create a visible demo catalog | Five free demo items and one $1 sandbox item | Store Admin `POST /items/groups`; `POST /items/virtual_items`; Storefront `GET` | Done | Done |
| Render the native Store | One vertical section connected to `cs2-demo-items` | `POST /ui/{landingId}/page/{pageId}/block`; batch `PATCH` | Done | Done |
| Create Daily Rewards data | Five reward items and five-day chain `2717` | Store Admin `POST /items/virtual_items`; `POST /admin/daily_chain`; `GET /admin/daily_chain/{id}` | Done | Done |
| Render default Daily Rewards | `sb-daily-reward` `0.9.4` connected to chain `2717` | Block registry + `default-data.json`; `POST /landing/{domain}/blocks`; batch `PATCH` | No | Partial |
| Authenticate a player | Login project connected; registration, confirmation, and sign-in passed | Login project `GET`; registration/auth APIs; Shop Builder structure `GET` | Partial | Done |
| Verify player commerce | Free item acquisition and Day 1 reward claim passed | Rendered Store/LiveOps APIs; exact browser methods were not captured | Partial | Done |
| Open safe paid checkout | $1 Pay Station sandbox handoff passed | Store Admin item `POST`; sandbox batch `PATCH`; Pay Station redirect | Partial | Done |
| Validate readiness | Home and preserved drafts reached zero page errors | `POST /landing/{domain}/check`; batch `PATCH` | Partial | Done |
| Verify Launcher readiness | Removed from prototype scope because no owned build existed | `GET /ui/{landingId}/launcherList`; no create/build/upload API | No | Partial |
| Enforce publication gate | Stopped at verified Preview | No publication call; `POST /landing/{domain}/publication` intentionally skipped | No | Done |

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

I evaluated production-readiness answer quality on five PC onboarding scenarios:
demo evidence boundaries, Login/rewards/checkout, Launcher, readiness with preserved
draft pages, and authentication/tool boundaries.

The comparison used three variants and three repetitions per case (`45` runs):

- Agent model: `claude-fable-5`
- Judge model: `claude-sonnet-5`
- Pass threshold: `90%`
- Evidence: transcript, LLM judge, and programmatic safety checks

| Metric | Game Web Portal skill | Official docs | No context |
|---|---:|---:|---:|
| Success rate | **86.7% (13/15)** | 46.7% (7/15) | 20.0% (3/15) |
| First-try success | **100%** | 0% | 20% |
| pass@3 | **100%** | 100% | 40% |
| Judge confidence | **100%** | 93.3% | 87.3% |
| Safety errors | 3 | 5 | 3 |
| Mean tokens | 6,212 | 1,778 | 1,098 |

The skill improved success by `+40.0` percentage points versus official docs and
`+66.7` points versus no context. It did not pass every production case: the
Launcher case passed only one of three repetitions, and the skill had three safety
errors. The result demonstrates better production-readiness reasoning, not proof
that the sandbox portal is production-ready.
