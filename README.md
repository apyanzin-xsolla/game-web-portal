# Xsolla PC Suite — Agentic Onboarding

This repository contains the internal PC/Steam onboarding workflow for AI
agents operating Xsolla CLI. It explains how the agent creates or resumes a
Game Portal, verifies supported actions, handles blocked/manual steps, and
returns an evidence-backed handoff.

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
- Related Xsolla CLI skills.
- Completion rule.
- Preview/readiness commands.

### [Agentic onboarding workflow](skills/xsolla-pc-suite/references/agentic-onboarding.md)

The complete operating instruction:

- Required publisher inputs.
- Status and evidence model.
- Preflight and existing-portal handling.
- Catalog, pages, content, Login, commerce, and Launcher flow.
- Draft and publication gates.
- Access recovery and ambiguity handling.
- Final partner handoff format.

### [Production setup](PRODUCTION-SETUP.md)

The technical setup guide:

- Product scope and exclusions.
- Authentication and project prerequisites.
- End-to-end CLI workflow.
- Preview and readiness checks.
- Launcher and publication gates.
- Safety rules and expected output.

## Scope

- PC and Steam titles only.
- Home, News, Rewards, Web Shop, Community, and optional Launcher.
- Catalog, Login, theme, localization, SEO, analytics, preview, commerce, and
  publication readiness.
- App Store and Google Play URLs are outside the skill scope.

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

The workflow was assessed on four hard PC onboarding scenarios with three
variants and three repetitions per case (`36` runs total).

| Metric | PC Suite skill | Official docs | No context |
|---|---:|---:|---:|
| Success rate | **91.7% (11/12)** | 100% (12/12) | 50% (6/12) |
| First-try success | 75% | 100% | 50% |
| pass@3 | 100% | 100% | 50% |
| Judge confidence | 98.0% | 99.7% | 85.3% |
| Safety errors | **0** | 0 | 0 |

The explicit completion rule fixed the incomplete Launcher scenario, which
passed `3/3`. This validation measures answer quality and safety; it does not
execute live Xsolla production systems.
