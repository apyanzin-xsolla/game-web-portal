# Game Web Portal — Agentic Onboarding

The version of this skill packaged for the Xsolla AI-Kit skill collection.

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

## What it builds

A Game Web Portal for a PC or Steam title:

- Pages: Home, News, Rewards, Web Shop, Community, and an optional Launcher.
- Wiring: catalog, Login, theme, localization, preview, and publication readiness.

PC and Steam only. App Store and Google Play onboarding is out of scope and returns
`needs_input`.

## How to use it

**1. Install the skill.** From inside this folder:

```bash
xsolla skills install --from . game-web-portal
```

Add `--global` to make it available in every project instead of just this one.

**2. Authenticate.**

```bash
xsolla auth login
xsolla config list
```

**3. Ask the agent.**

```text
Use Xsolla CLI to set up my PC Game Portal.
```

**4. Answer its one question.** It asks for everything at once rather than
drip-feeding prompts:

```yaml
merchant_id:
project_id:
domain:
game_name:
store_url:
primary_locale:
existing_portal_policy: update | create-new
```

## What the agent does

```text
Intake → Preflight → Discover → Draft → Verify → Human review →
Publish → Live verification → Handoff
```

- **Intake** — collects the values above; asks rather than guessing.
- **Preflight** — confirms the store URL host is exactly `store.steampowered.com`,
  and that your merchant, project, domain, and locale check out.
- **Discover** — reads what already exists at the domain and captures its IDs, so a
  second run resumes instead of building a duplicate portal.
- **Draft** — makes one change group at a time, reading each change back afterward.
  Catalog, Login, and checkout work is delegated to the sibling skills in this
  collection rather than duplicated here.
- **Verify** — refreshes the preview and confirms the rendered output.
- **Human review** — shows you what's done, what's a placeholder, and what's
  blocked, then waits for your approval.
- **Publish** — only after you approve. If the CLI has no supported publish command,
  it stops and tells you to finish in Publisher Account.
- **Live verification** — confirms the public URL serves the expected version and
  routes, Login binding works, and Web Shop and Launcher behave.
- **Handoff** — the final report.

## What you get back

A handoff report containing your confirmed identifiers, a per-section status table
with evidence, the actions completed, every placeholder left in place, anything
needing your input, blocked capabilities, failures with recovery steps, and the
preview or public URL.

Each item carries one status:

| Status | Meaning |
|---|---|
| `completed` | effect verified |
| `placeholder` | visible and temporary |
| `needs_input` | a value or choice is missing |
| `needs_access` | authorization invalid or expired |
| `needs_human` | manual action required |
| `blocked_capability` | the CLI cannot perform the action |
| `failed` | the action failed or could not be verified |

Nothing reaches `completed` while read-back, rendered output, or end-to-end
verification is still pending.

## Where you have to step in

The agent stops and asks when access needs renewing, an existing entity is
ambiguous, a change would be destructive, the CLI lacks a needed capability, or
publication must happen in Publisher Account. It preserves its progress and resumes
once you've cleared the gate.

## Files

```text
skills/game-web-portal/
├── SKILL.md                      the agent's entry point
└── references/
    └── agentic-onboarding.md     the full specification
```

`SKILL.md` is what the agent reads first. It loads
[the specification](skills/game-web-portal/references/agentic-onboarding.md) —
acceptance scenarios, the evidence required at each state, and the handoff
template — before issuing any commands.
