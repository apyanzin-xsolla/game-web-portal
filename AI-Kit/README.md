# AI-Kit — marketplace-source version

The version of the `game-web-portal` skill shaped for
[`xsolla/xsolla-ai-kit`](https://github.com/xsolla/xsolla-ai-kit), the public skills
repository the Xsolla CLI reaches as a remote source rather than an embedded bundle.

```text
AI-Kit/
└── skills/
    └── game-web-portal/
        ├── SKILL.md                       126 lines
        └── references/
            └── agentic-onboarding.md      185 lines
```

## Conventions this version follows

From `CONTRIBUTING-skills.md` in `xsolla-ai-kit`:

| Rule | How this version meets it |
|---|---|
| One skill per directory — `skills/<name>/SKILL.md` | yes |
| Under 200 lines, overflow into `references/*.md` | 126 |
| YAML frontmatter with `description: >-` block | yes |
| `metadata:` with `owner` and `domain` | `owner: apyanzin-xsolla`, `domain: orchestrator` |
| Pushy description covering every trigger scenario | full trigger list plus the out-of-scope statement |
| Required section `## When to use` | trigger keywords and entry conditions |
| Required section `## Prerequisites` | auth, merchant/project, API key, access, required input block |
| Required section `## Steps` | the nine-state run flow with delegation targets |
| Required section `## Common pitfalls` | six failure modes with fixes |
| No raw `curl` | no HTTP; the flow delegates to CLI-backed sibling skills |

`domain: orchestrator` follows `shop-setup`, which the skills table lists as
"Orchestrator — full zero-to-shop flow". This skill is the PC/Steam portal analogue.

Delegation targets are named in ai-kit's own vocabulary — `merchant-setup`,
`shop-setup`, `catalog-design`, `login-setup`, `headless-checkout-integration` — not the
CLI bundle's names.

## Try it from this folder

```bash
xsolla skills install --from github:apyanzin-xsolla/game-web-portal#AI-Kit/skills \
  --allow-untrusted game-web-portal
```

Once merged into `xsolla-ai-kit`:

```bash
xsolla skills install --from github:xsolla/xsolla-ai-kit#skills game-web-portal
```

The shorter `--source ai-kit` form does not work on CLI 1.9.3 — the built-in source
points at `github.com/xsolla/ai-kit` on `master`, while the repository is
`xsolla/xsolla-ai-kit` on `main`, so the fetch returns HTTP 404. Register the correct
spec locally to work around it:

```bash
xsolla skills sources add ai-kit github:xsolla/xsolla-ai-kit#skills --force
```

## Known caveat: `>-` and `xsolla skills list`

The CLI's frontmatter reader does not parse YAML block scalars, so this skill's
description displays as a bare `>-` in `xsolla skills list`. That affects every skill
in `xsolla-ai-kit` today, not just this one — the format is mandated by
`CONTRIBUTING-skills.md`, so this version keeps it for consistency. Agents read
`SKILL.md` directly and are unaffected; only the CLI's listing output is degraded. The
`CLI/` version uses a single-line description and lists correctly.

## Upstream status

Not yet opened, but unblocked: the repository is public with forking enabled, so a
personal fork plus a cross-fork PR to `main` works without any access grant. Account
access to the upstream is read-only, so the branch has to live in a fork — note the
repo has no forks yet and all PRs to date came from in-repo branches.
