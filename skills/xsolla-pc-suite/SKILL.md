---
name: xsolla-pc-suite
description: Set up, create, configure, or resume an Xsolla PC Suite portal for a PC or Steam game with Xsolla CLI; use when the user says "set up my PC Game Portal", "create my PC Suite", "build a Steam game portal", or asks for Home, News, Rewards, Web Shop, Community, Launcher, Login, preview, or publication readiness.
---

# Xsolla PC Suite

Use Xsolla CLI to create or resume a Game Portal, verify every supported
change, and return an evidence-backed handoff.

## Scope

Xsolla PC Suite is PC-only. Accept Steam store URLs; reject App Store and
Google Play URLs with `needs_input` and explain that Mobile onboarding is
outside this skill.

## Entry point

The skill is bundled with Xsolla CLI. Install it into the user's agent:

```bash
xsolla skills install xsolla-pc-suite
```

The user then asks their agent:

```text
Use Xsolla CLI to set up my Game Portal.
```

The agent must load
[references/agentic-onboarding.md](references/agentic-onboarding.md) before
issuing commands and follow every status and verification gate in that file.

## Start every run

1. Show and confirm the active CLI context:

   ```bash
   xsolla config list
   ```

2. If authentication is missing, run:

   ```bash
   xsolla auth login
   ```

3. Discover existing sites before creating anything:

   ```bash
   xsolla shopbuilder list-websites --json
   ```

4. If merchant/project setup is incomplete, use the
   **publisher-onboarding** skill first.

5. Use the **shopbuilder** skill for site/page/block/theme/localization/preview
   commands, and its references for the verified command sequence.

## Related CLI skills

- **publisher-onboarding** — merchant, project, and API-key readiness.
- **shopbuilder** — website structure, blocks, theme, localization, preview.
- **catalog-admin** — real Store catalog and pricing.
- **login-debug** — Login configuration and verification.
- **payment-flow** / **webshop-checkout** — sandbox payment verification.

Do not invent commands. If the current CLI does not expose a required action,
record `blocked_capability` or `needs_human` and continue according to the
reference workflow.

## Completion rule

Never place an item under **Completed** while read-back, rendered output, or
end-to-end verification is pending; use `needs_input`, `needs_human`,
`blocked_capability`, or `failed` instead.

## Finish

Generate a fresh preview and readiness check:

```bash
xsolla shopbuilder enable-preview --slug <domain>
xsolla shopbuilder preview-link --slug <domain>
xsolla shopbuilder verify-website --slug <domain>
```

Publication remains an explicit human gate unless a supported CLI publication
command exists in the installed version.

## Examples

- "Use Xsolla CLI to set up my PC Game Portal for this Steam title."
- "Create an Xsolla PC Suite site with Home, Rewards, Web Shop, and Community."
- "Resume my existing Game Portal without duplicating pages."
- "Prepare my PC Suite portal and give me a verified preview."
