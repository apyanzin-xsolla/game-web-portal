# Xsolla Game Web Portal — Production Setup

## Purpose

Xsolla Game Web Portal Agentic Onboarding helps an AI agent create or resume a
PC Game Portal through Xsolla CLI while verifying supported actions and
reporting manual, blocked, or failed work explicitly.

## Specification model

The workflow uses two agent documents:

- `SKILL.md` — stable trigger, CLI bootstrap, scope, and constitution.
- `references/agentic-onboarding.md` — requirements, state/evidence design,
  acceptance scenarios, and run checklist.

CLI command recipes remain owned by the related bundled skills and are not
duplicated here.

## Scope

Included:

- Steam/PC titles.
- Existing portal discovery and safe resume.
- Home, News, Rewards, Web Shop, Community, and optional Launcher.
- Catalog, Login, presentation, preview, readiness, and publication gates.
- Evidence-backed partner handoff.

Excluded:

- App Store and Google Play onboarding.
- Mobile purchase-return flows.
- Fabricated identifiers, content, commands, or completion states.
- Automatic publication when no supported CLI command exists.

## Runtime prerequisites

- Xsolla CLI containing the bundled `game-web-portal` skill.
- Authenticated publisher context.
- Confirmed merchant ID and project ID.
- API key for catalog/payment operations where required.
- Shop Builder access for the target project.
- Approved content and brand assets.
- Steam title and target domain.

## Required onboarding input

```yaml
merchant_id:
project_id:
domain:
game_name:
store_url:
primary_locale:
existing_portal_policy: update | create-new
```

Optional:

- Approved logo, hero, screenshots, colors, and fonts.
- Existing Store catalog.
- Additional locales.
- Analytics integration IDs.
- Existing Launcher and build.

## Operational contract

Each run follows the state flow defined in the workflow reference:

```text
Intake → Preflight → Discover → Draft → Verify → Human review →
Publish → Live verification → Handoff
```

Every mutation is preceded by an existing-vs-desired-state check and followed
by read-back, preview refresh, and ledger update.

## Status model

- `completed`
- `placeholder`
- `needs_input`
- `needs_access`
- `needs_human`
- `blocked_capability`
- `failed`

An item must not appear under **Completed** while read-back, rendered output, or
end-to-end verification is pending.

## Evidence requirements

### Draft-ready

- Correct project, domain, landing type, and locale.
- No duplicate routes or blocks.
- Saved and rendered content verified.
- Every placeholder reported.
- Login and commerce verified or explicitly incomplete.
- No unresolved readiness failure.

### Launcher

- Real Launcher belongs to the project.
- Game build is uploaded.
- Installer is generated.
- Installer download is verified.

### Published-verified

- Explicit publication approval.
- Public URL responds successfully.
- Expected version and routes are live.
- Login and account binding work.
- Web Shop and Launcher outcomes are verified.

HTTP 200 with stale content is not completion.

## Human gates

Human action is required when:

- Access needs renewal.
- Existing entities are ambiguous.
- A destructive change needs approval.
- The installed CLI lacks a supported capability.
- Publication must be completed in Publisher Account.

The agent preserves the ledger and resumes after the gate is cleared.

## Final output

The partner handoff contains:

- Confirmed merchant ID, project ID, domain, Steam URL, and locale.
- Overall status.
- Portal and page IDs.
- Section statuses and evidence.
- Completed actions.
- Placeholders.
- Required human actions.
- Blocked capabilities.
- Failures and recovery actions.
- Preview/public URL and publication evidence.

