# Changelog

All notable changes to this skill will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [0.1.0] - 2026-08-10

### Added
- Initial skill for PC/Steam Game Web Portal onboarding — creates or resumes a verified
  portal (Home, News, Rewards, Web Shop, Community, and optional Launcher) through
  `xsolla shopbuilder` and returns an evidence-backed partner handoff. Scope is PC and
  Steam only; App Store and Google Play URLs return `needs_input`.
- Constitution applied to every run: inspect before mutation, read back after mutation,
  never invent commands or completion evidence, and never report an item as completed
  while read-back or end-to-end verification is pending.
- `references/agentic-onboarding.md` — the full specification: `GIVEN / WHEN / THEN`
  acceptance scenarios, state flow, status model, evidence contract, existing-vs-desired
  state handling, and the draft, publication, and live-verification gates.
