# Xsolla PC Suite — Production Setup

## Purpose

Xsolla PC Suite Agentic Onboarding helps an AI agent create or resume a PC Game
Portal through Xsolla CLI while verifying supported actions and reporting
manual, blocked, or failed work explicitly.

## Scope

Included:

- Steam/PC titles.
- Existing portal discovery and safe resume.
- Home, News, Rewards, Web Shop, Community, and optional Launcher.
- Catalog, Login, theme, localization, SEO, analytics, preview, and readiness.
- Evidence-backed handoff and publication gate.

Excluded:

- App Store and Google Play onboarding.
- Mobile purchase-return flows.
- Fabricated identifiers, catalog data, assets, commands, or completion states.
- Automatic publication when no supported CLI command exists.

## Entry point

```bash
xsolla skills install xsolla-pc-suite
```

Then ask:

```text
Use Xsolla CLI to set up my PC Game Portal.
```

## Prerequisites

- Xsolla CLI containing the bundled skill.
- Authenticated publisher:

  ```bash
  xsolla auth login
  ```

- Confirmed context:

  ```bash
  xsolla config list
  ```

- Merchant ID, project ID, Steam URL, domain, game name, and locale.
- API key for Basic-auth catalog/payment operations where required.
- Approved content and brand assets.

## Start every run

```bash
xsolla config list
xsolla shopbuilder list-websites --json
```

The agent confirms the active project and checks for an existing domain before
any mutation.

If merchant/project setup is incomplete, use `publisher-onboarding`.

## Workflow

1. Validate the exact Steam hostname; reject Mobile or spoofed URLs.
2. Inspect the existing portal and apply update/create policy.
3. Select only a supported landing/template type.
4. Create or resume pages and blocks through `shopbuilder`.
5. Build/inspect real catalog data with `catalog-admin`.
6. Configure Home, News, Rewards, Web Shop, Community, and optional Launcher.
7. Verify Login with `login-debug`.
8. Verify supported payment/checkout behavior.
9. Apply approved theme, localization, SEO, and analytics.
10. Generate preview and run readiness checks.
11. Present a human review before publication.
12. Verify the live version after publication.

## Preview and readiness

```bash
xsolla shopbuilder enable-preview --slug <domain>
xsolla shopbuilder preview-link --slug <domain>
xsolla shopbuilder verify-website --slug <domain>
```

`draft_ready` requires:

- Correct project, domain, type, and locale.
- No duplicate routes or blocks.
- Verified saved/rendered content.
- Every placeholder reported.
- Login and commerce verified or explicitly incomplete.
- No unresolved readiness failures.

## Launcher gate

Launcher is complete only when:

- A real Launcher belongs to the project.
- A game build is uploaded.
- An installer is generated.
- Installer download is verified.

The publisher cannot override this gate with “publish anyway.”

## Publication gate

Publication requires explicit approval. If the installed CLI has no supported
publication command, the status is `needs_human` and an authorized user
publishes in Publisher Account.

`published_verified` requires:

- Public URL responds successfully.
- Expected version and routes are live.
- Login works.
- Web Shop and Launcher outcomes are verified.

HTTP 200 with stale content is not completion.

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

## Final handoff

The report includes:

- Overall status.
- Domain, landing ID, type, and catalog source.
- Page IDs, routes, statuses, and evidence.
- Completed actions.
- Placeholders.
- Required publisher/human actions.
- Blocked capabilities.
- Failures and recovery actions.
- Preview/public URL and publication evidence.

