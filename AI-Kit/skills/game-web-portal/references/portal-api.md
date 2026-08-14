# Xsolla Game Web Portal — Site Builder API reference

The portal itself (websites, pages, blocks, theme, copy, domain, preview) is
Site Builder. This file is the API surface for Step 4 (Draft) and Step 5
(Verify). Catalog, Login, and checkout stay delegated — see `SKILL.md`.

## Context

- **Base URL:** `https://sitebuilder.xsolla.com`
- **Auth:** publisher session cookie (`pa-v4-token` from `publisher.xsolla.com`),
  **not** `XSOLLA_PROJECT_API_KEY`. A missing, stale, or unauthorized session
  returns `403 not_enough_permissions` → `needs_access`.
- **Path shorthand below:** `{M}` = `/api/merchant/{merchant-id}/project/{project-id}`

### Two different IDs — the top cause of hard failures

| Key | What it is | Used by |
|---|---|---|
| `slug` | the domain label, e.g. `voidwall` → `voidwall.xsolla.site` | `landing/{slug}/…`, localization, preview, versions |
| `landingId` | the landing's Mongo `_id` (top-level `_id` in `structure`) | `ui/{landing-id}/…` (blocks, batch), `assets/{landing-id}/…` |

Passing a slug into a `ui/*` path makes the backend parse it as an ObjectId and
return **500**. Resolve `landingId` once during Discover and reuse it.

## Discover

| Intent | Call |
|---|---|
| List portals in the project | `GET {M}/landings` |
| Read one portal's metadata (incl. `_id` = `landingId`) | `GET {M}/landing/{slug}` |
| Read full structure — pages, blocks, IDs, ordering | `GET {M}/landing/{slug}/structure` |
| List pages | `GET {M}/landing/{slug}/pages` |
| Read one page | `GET {M}/landing/{slug}/pages/{page-id}` |
| Read one block, localizations inlined | `GET {M}/landing/{slug}/blocks/{block-id}` |
| Licensing agreements (publication gate) | `GET /merchant/current/merchants/{merchant-id}/agreements` |

Discover is mandatory before any mutation: it supplies `landingId`, page IDs,
block IDs, and the current ordering, and it is how resume avoids duplicate pages.

## Draft — create the portal

A fresh portal is **empty and unconfigured** until all three calls run in order.

| Step | Call | Body |
|---|---|---|
| 1. Create | `POST {M}/landing/{slug}` | `{ "name": "<Name>", "type": "topup", "colorScheme"?: "<name>", "theme"?: {…} }` |
| 2. Finalize type | `PUT {M}/landing/{slug}/admin/change-landing-type` | `{ "type": "topup" \| "store" \| "sellingpage" }` |
| 3. Add a page | `POST {M}/landing/{slug}/pages` | `{ "name": "<1–80 chars>", "path": "/main" }` |

- Create returns a landing with `pages: []` and `type: null`. Without step 2 the
  editor gates on a domain prompt and the preview 404s.
- `path` accepts lowercase `a–z`, `0–9`, hyphen and slash only, max 80 chars.
- A new page is scaffolded with default template blocks — read them back with
  `structure` and trim rather than adding on top.
- Other portal-level calls: `POST {M}/landing/{slug}/duplicate`,
  `DELETE {M}/landing/{slug}` (destructive — never without explicit approval).

## Draft — sections and blocks

All keyed by `landingId`, not slug.

| Intent | Call | Body |
|---|---|---|
| Add block | `POST {M}/ui/{landing-id}/page/{page-id}/block` | `{ "block": "<module>", "index"?: <0-based> }` |
| Move block | `PUT {M}/ui/{landing-id}/page/{page-id}/block` | source/destination indices, 0-based |
| Delete block | `DELETE {M}/ui/{landing-id}/page/{page-id}/block` | block `_id` |
| Duplicate block | `POST {M}/ui/{landing-id}/page/{page-id}/block/duplicate` | `{ "blockId": "<_id>", "index"?: <n> }` |
| Patch block / page / site | `PATCH {M}/ui/{landing-id}/batch` | see below |

`block` is a **module template name**, not a block ID. Read the modules actually
available to the project from `structure` before adding — do not guess names for
News, Rewards, or Community sections. Known modules include `lead` (hero),
`newStore` (catalog grid), `federated`, `faq`, and the default page scaffold
(header, lead, description, packs, bento, gallery, requirements, faq, footer).

The batch endpoint is the same call the editor uses. Body is a map of
`requestId → change`:

```json
{"r1": {"type": "block", "id": "<blockId>",
        "patches": [{"op": "replace", "path": ["hidden"], "value": true}]}}
```

- `type` is `block` | `page` | `site`; `id` is the block `_id`, page `_id`, or
  the `landingId` (site-level).
- `path` is an Immer segment array. `op` is `add` | `remove` | `replace`.
- Protected, un-patchable: `_id`, `module`, `blockVersion`.

## Draft — theme and assets

Theme has no dedicated endpoint; it is a `site` patch through the batch call:

```json
{"t": {"type": "site", "id": "<landingId>",
       "patches": [{"op": "replace",
                    "path": ["theme", "mainColors", "accentColor"],
                    "value": "rgba(53,224,255,1)"}]}}
```

| Intent | Call |
|---|---|
| List image/font assets | `GET {M}/assets/{landing-id}/site` |
| Upload asset (`multipart/form-data`, part `file`) | `POST {M}/assets/{landing-id}/site` |
| Delete asset | `DELETE {M}/assets/{landing-id}/{asset-id}` |

Upload only partner-approved assets. Never reuse assets found on another portal.

## Draft — copy and localization

**Block text does not live on the block.** Blocks reference an `L:` id and the
text lives in the localization store, so patching `["values","title"]` through
the batch endpoint does nothing. Set copy here instead.

| Intent | Call | Body |
|---|---|---|
| Read the whole store (all locales/pages) | `GET /api/localization/extract/{slug}` | — |
| Set one string | `POST /api/localization/update/{slug}` | `{ "pageId": "<pageId>", "id": "L:<uuid>", "locale": "en-US", "value": "<p>…</p>" }` |
| Set many for one locale | `POST /api/localization/update-many/{slug}` | `{ "locale": "en-US", "perScopeValues": { "<pageId>": { "L:<id>": { "translation": "<p>…</p>" } } } }` |
| Add a locale | `POST {M}/landing/{slug}/language` | `{ "language": "en-US" }` |
| Remove a locale | `DELETE {M}/landing/{slug}/language` | `{ "language": "en-US" }` |

- Namespaces: page strings live under `pages.<pageId>.texts."L:<id>"`, shared
  strings under `common."L:<id>"` (pass `common` as the scope key).
- Keep the `L:` prefix on ids. Values are HTML — wrap copy in tags.
- In `update-many` the per-id value **must** be `{ "translation": "<html>" }`.
  Any other shape (bare string, `value`, `text`, `translations`) returns 200 and
  writes an **empty** string for that locale — destructive. Other locales on the
  same string are preserved.

## Domain, analytics, access

| Intent | Call | Body |
|---|---|---|
| Attach external domain | `POST {M}/landing/{slug}/domains` | `{ "domain": "shop.example.com" }` |
| Change domain | `PATCH {M}/landing/{slug}/domains` | `{ "domain": "…" }` |
| Remove domain | `DELETE {M}/landing/{slug}/domains` | — |
| Verify DNS | `GET {M}/landing/{slug}/domains/lookup` | — |
| Add analytics connector | `PUT {M}/landing/{slug}/applications` | `{ "type": "gtm" \| "ga", "value": "<id>" }` |
| Remove connector | `DELETE {M}/landing/{slug}/applications` | `{ "type": "gtm" }` |
| Update access restrictions | `PATCH {M}/landing/{slug}/restrictions` | restriction set |
| Clear restrictions | `DELETE {M}/landing/{slug}/restrictions` | — |

## Verify and preview

| Intent | Call |
|---|---|
| Enable public preview | `GET /api/landing/{slug}/public-preview/enable-preview` |
| Disable public preview | `GET /api/landing/{slug}/public-preview/disable-preview` |
| Current preview token/link | `GET /api/landing/{slug}/public-preview/public-preview-last-token` |
| Readiness check before publish | `GET {M}/landing/{slug}/check` |

The preview link is `https://sitebuilder.xsolla.com/preview/{slug}`, valid only
when the token response reports `enabled` with a `lastToken`. Re-read
`structure` after every change group; a mutation response alone is not evidence.

## Publish and rollback

**There is no publish endpoint.** Publication is `needs_human`: request explicit
approval, hand the partner the exact manual step, and resume with live
verification once they confirm. Never report a portal as published on the
strength of a 200 from any other call.

| Intent | Call |
|---|---|
| List archived versions | `GET {M}/landing/{slug}/versions` |
| Apply an archived version (rollback) | `PUT {M}/landing/{slug}/version/{version-id}` |

## Known API issues

Confirmed on a live portal, 2026-08-14. Both are tooling-side, not spec-side —
report them as `failed` with the response, never as `completed`:

- The public preview URL can return **403** while the token response reports the
  preview as enabled. Fall back to a structure read-back as Verify evidence and
  disclose the preview gap in the handoff.
- `GET .../landing/{slug}/check` can return **400** for a missing
  `draftPagesIds`, which the documented request does not carry. When it does,
  readiness is `blocked_capability`; verify pages and blocks individually
  instead and say so in the report.

## Failure → status mapping

| Response | Status | Action |
|---|---|---|
| `401` / `403 not_enough_permissions` | `needs_access` | preserve the ledger, reauthenticate, re-read state, resume |
| `404 merchant_projects_info_not_found` on create | `needs_human` | Shop Builder is not enabled for the project; the partner enables it in Publisher Account |
| `500` from a `ui/*` path | — | wrong key: a slug was sent where `landingId` is required. Fix and retry; this is not a capability block |
| Publish request | `needs_human` | no endpoint exists |
