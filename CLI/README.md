# CLI — bundled-skill version

The version of the `game-web-portal` skill shaped for
[`xsolla/xsolla-cli`](https://github.com/xsolla/xsolla-cli), where skills are compiled
into the binary by `//go:embed skills` and ship with `brew install`.

```text
CLI/
└── skills/
    └── game-web-portal/
        ├── SKILL.md                        89 lines
        ├── VERSION                         0.1.0
        ├── CHANGELOG.md
        └── references/
            └── agentic-onboarding.md      185 lines
```

## Conventions this version follows

From `CONTRIBUTING.md` and `skills/README.md` in `xsolla-cli`:

| Rule | How this version meets it |
|---|---|
| One `SKILL.md` per skill directory | yes, with `references/` for the specification |
| Explicit `Use when ...` trigger in frontmatter | `description` ends with `Use when the request involves …` |
| Inline `## Examples` | yes |
| Under ~200 lines | 89 |
| `references/` only when it adds real value | the 185-line spec is loaded after platform and CLI context are confirmed |
| Per-skill `VERSION` + `CHANGELOG.md` | `0.1.0`, matching the `shopbuilder` and `catalog-admin` starting point |
| Single-line `description` | required — the CLI's frontmatter parser does not read YAML `>-` block scalars |

Section headings are freeform (`Scope`, `Entry point`, `Constitution`, `Bootstrap`,
`Finish`, `Examples`), matching `shopbuilder` — the most developed bundled skill —
rather than the four headings named in `skills/README.md`, which no shipped skill
follows literally.

## Try it from this folder

```bash
xsolla skills install --from github:apyanzin-xsolla/game-web-portal#CLI/skills \
  --allow-untrusted game-web-portal
```

`--allow-untrusted` is required because the CLI's trusted allowlist covers
`github.com/xsolla/*` and configured named sources only.

Once merged into `xsolla-cli`, it becomes part of the embedded bundle:

```bash
xsolla skills install game-web-portal
```

## Upstream status

Prepared as commit `feat: add game-web-portal agent skill` against `xsolla-cli`'s `dev`
branch — the skill directory plus one row in `skills/README.md`. Not yet opened: the
repo is private with forking disabled at both repo and org level, and account access is
read-only, so no branch can be pushed. Needs a CODEOWNER
(`@XoRohan @JavadManashti @s-lim94 @eh-xsolla`) to grant write.
