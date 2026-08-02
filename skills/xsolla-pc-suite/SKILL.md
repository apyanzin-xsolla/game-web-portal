---
name: xsolla-pc-suite
description: Set up, create, configure, or resume an Xsolla PC Suite portal for a PC or Steam game with Xsolla CLI; use for PC Game Portal, Web Shop, Launcher, Login, preview, readiness, or publication requests.
---

# Xsolla PC Suite

Use Xsolla CLI to create or resume a verified PC Game Portal and return an
evidence-backed handoff.

## Scope

- PC/Steam only.
- App Store and Google Play URLs return `needs_input`.
- Load the full workflow reference only after platform and CLI context are
  confirmed.

## Entry point

Install the bundled skill:

```bash
xsolla skills install xsolla-pc-suite
```

The user asks:

```text
Use Xsolla CLI to set up my PC Game Portal.
```

## Constitution

These rules apply to every run:

1. Inspect before mutation; read back after mutation.
2. Never invent commands, IDs, values, content, or completion evidence.
3. Never choose an ambiguous match—ask the user.
4. Stop on access failure or unsupported capability.
5. Never put an item under **Completed** while read-back, rendered output, or
   end-to-end verification is pending.
6. Ask for approval before destructive changes or publication.

## Bootstrap

Confirm the active context:

```bash
xsolla config list
xsolla shopbuilder list-websites --json
```

If authentication is missing:

```bash
xsolla auth login
```

Then load
[references/agentic-onboarding.md](references/agentic-onboarding.md) and follow
its requirements, state model, and run checklist.

## Related skills

- **publisher-onboarding** — merchant, project, and API-key readiness.
- **shopbuilder** — site structure, blocks, theme, localization, preview.
- **catalog-admin** — Store catalog and pricing.
- **login-debug** — Login configuration and verification.
- **payment-flow** / **webshop-checkout** — checkout verification.

If a required CLI command is unavailable, return `blocked_capability` or
`needs_human`; never invent syntax.

## Finish

```bash
xsolla shopbuilder enable-preview --slug <domain>
xsolla shopbuilder preview-link --slug <domain>
xsolla shopbuilder verify-website --slug <domain>
```

Publication remains a human gate unless the installed CLI exposes a supported
publication command.

## Examples

- "Set up my PC Game Portal for this Steam title."
- "Resume my PC Suite portal without duplicating pages."
- "Prepare my portal and give me a verified preview."
