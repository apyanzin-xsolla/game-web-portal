# Xsolla PC Suite — Agentic Onboarding Workflow

Use only current Xsolla CLI commands, connected Xsolla capabilities, and
official documentation. Never simulate an unsupported action.

## Required input

Collect all missing values in one question:

```yaml
merchant_id:
project_id:
domain:
game_name:
store_url:
primary_locale:
existing_portal_policy: update | create-new
```

Optional: approved brand assets, analytics IDs, and additional locales.

Never generate or reuse partner identifiers, domains, credentials, products,
prices, assets, or URLs.

## Status model

- `completed` — effect verified with evidence.
- `placeholder` — visible, explicitly temporary content.
- `needs_input` — required value or choice is missing.
- `needs_access` — authorization is expired or insufficient.
- `needs_human` — an authorized person must act.
- `blocked_capability` — the installed CLI cannot perform the action.
- `failed` — supported action failed or cannot be verified.

Maintain a ledger containing step, status, entity IDs, evidence, blocker, and
next action; resume at the first incomplete step.

## Hard rules

1. Inspect before mutation; read back after mutation.
2. Use only currently available templates, components, fields, and values.
3. Never choose the first ambiguous site, page, component, or data match—ask.
4. Never duplicate or overwrite an existing portal without approval.
5. Stop on access errors and return `needs_access`.
6. A command exit is not completion; only observed effects count.
7. Unsupported Login, purchase, Launcher, or publication becomes a human or
   capability gate, never simulated success.
8. Never say “All done” while anything is placeholder, blocked, manual, failed,
   or unverified.
9. Never list an item under **Completed** while read-back, rendered output, or
   end-to-end verification is pending; use `needs_input`, `needs_human`,
   `blocked_capability`, or `failed` instead.

## Workflow

### 1. Preflight

Confirm CLI merchant/project context before mutation. Parse the store URL and
compare the exact hostname:

- `store.steampowered.com` → PC
- `apps.apple.com` or `play.google.com` → `needs_input` (outside PC Suite scope)

Reject invalid, unknown, and spoofed hosts such as
`store.steampowered.com.example.com`. Do not infer platform from the game name.

List existing websites. If the domain exists, read its structure and follow
`existing_portal_policy`; if multiple matches exist, return `needs_input`.

### 2. Discover

Inspect current CLI help, official skills, and available structures.

- Select only an available portal template or landing type.
- Use store import only when a supported CLI workflow exists.
- Record the selected type, reason, capabilities, and limitations.
- If no suitable type exists, return `blocked_capability` or ask before using
  the closest option.

### 3. Create or resume

Create a portal only after preflight succeeds. Follow the **shopbuilder** skill
bootstrap sequence, then read the structure and verify merchant, project,
domain, type, locale, landing ID, page IDs, and block IDs.

For an existing portal, continue from its first incomplete element. Re-running
must not create duplicate pages or blocks.

### 4. Catalog and brand

Use only observed catalog responses or publisher-approved content.

- Empty, partial, failed, or rate-limited import → stop affected work and
  report `needs_input` or `blocked_capability`.
- Never create sample products or prices.
- Placeholders require publisher approval and visible labeling.
- Build/inspect the real catalog with **catalog-admin** before wiring Web Shop.

### 5. Portal structure

Required sections:

- Home — approved assets and claims only.
- News — approved source or “News coming soon” placeholder.
- Rewards — real reward/offer identifiers or “Rewards coming soon” placeholder.
- Web Shop — real catalog products, prices, groups, and currencies only.
- Community — approved destination or “Community coming soon” placeholder.

Launcher is PC-only and `completed` only when a real Launcher, uploaded build,
generated installer, and working download are verified. Discovery alone is not
configuration.

For each page/block: inspect structure, use supported fields, apply targeted
patches, read back, and refresh preview.

### 6. PC commerce

Configure Web Shop from real catalog data. Launcher remains optional and
evidence-gated; completion requires an uploaded build, generated installer, and
working download.

Use **payment-flow** or **webshop-checkout** for supported sandbox verification.
Missing configuration/evidence returns `needs_input`, `needs_human`, or
`blocked_capability`.

### 7. Login

Use **login-debug** for supported Login setup and verification.

Login is `completed` only when:

- Login UI renders.
- Sign-in completes.
- Callback and session are valid.
- Required account binding works.

Otherwise return `needs_human` or `blocked_capability` with the exact action and
verification method.

### 8. Presentation

Apply only approved theme, SEO, localization, and analytics values through
supported CLI commands. Verify every saved change and refreshed preview.

### 9. Draft gate

Set `draft_ready` only when:

- Ownership, domain, type, and locale are correct.
- Required routes exist with no duplicates.
- Saved and rendered content is verified where possible.
- Every placeholder is visible and reported.
- Login and PC commerce are verified or explicitly incomplete.
- `verify-website` completes without unresolved readiness failures.

Draft-ready does not mean published.

### 10. Publication gate

List placeholders, blockers, manual actions, and failures; ask for explicit
publication approval.

Publish only through an authorized CLI command available in the installed
version. Otherwise return `needs_human` and direct the user to Publisher
Account, then resume after publication.

Set `published_verified` only after confirming:

- Public URL returns successfully.
- Expected version and routes are live.
- Login outcome is verified.
- Purchase or Launcher outcome is verified.

A successful response showing stale content is not completion.

## Error and conflict handling

- `401`, `403`, or expired access → preserve the ledger, return
  `needs_access`, reauthenticate, and resume.
- “Publish anyway” → keep failed gates, refuse false completion, and explain
  the required evidence.
- Partial prior run → read current state and continue idempotently.
- Ambiguous match → ask the publisher; do not choose automatically.
- Unknown CLI command → check `--help` and related skills; never invent syntax.

## Final handoff

```markdown
# Xsolla PC Suite onboarding report

Overall status: draft_ready | needs_input | needs_access | needs_human |
blocked_capability | published_verified | failed

## Portal
- Platform:
- Domain:
- Landing ID:
- Landing type:
- Catalog source:

## Sections
| Section | Page ID | Route | Status | Evidence |
|---|---|---|---|---|

## Completed
- Verified action + evidence

## Placeholders
- Temporary content + visible label

## Needs input / human action
- Action + owner + required value + verification method

## Blocked capabilities
- Missing capability + impact + safe next step

## Failed
- Action + error + recovery step

## Publication
- Status:
- Preview/public URL:
- Verification evidence:
```

Honest partial completion is correct. Simulated full completion is failure.
