# AGENTS.md — pod-chrome-headless

Standalone candy repo for the `chrome-headless` candy — the real
`google-chrome` binary in `--headless=new` mode as a cross-deployment CDP driver
(internal CDP on `9223`, published `9222` through the `chrome-cdp` proxy). The
entire candy lives in `charly.yml` at the repo root: the `chrome-headless:` entity
with its `require`, `candy:` composition, `service:`, and `plan:` (the launcher is
authored inline). There is no source tree.

Canonical files:

- `charly.yml` — the `chrome-headless` candy entity (description, `require`,
  `candy`, service, plan).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-check:cdp` — the owning procedure: the declarative `cdp:` check verb
  this candy is a driver for. Load before editing, building, deploying, or
  troubleshooting this candy.
- `/charly-selkies:chrome-cdp` — the CDP proxy layer this candy composes.
- `/charly-selkies:chrome` — the parent Chrome layer providing the binary.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs, service declarations).
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own; the sibling `/charly-check:cdp`
covers the surface. The gap is routed to the named skill-authoring batch
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The live R10 witness is a composing check bed; the candy's own `check:` steps
  assert the launcher's `--headless=new`, `--remote-allow-origins`, and
  `remote-debugging-port=9223` flags, the real binary, the running service, and
  that `/json/version` returns `200` with a `webSocketDebuggerUrl` through the
  proxy.

## Modify this repo

- Edit the `chrome-headless:` candy entity in `charly.yml`; the launcher is the
  inline `write:` step in the plan. Keep the flag strings the plan's `check:`
  steps lock in.
- The `--remote-allow-origins='*'` flag is load-bearing for Chrome 146+ CDP
  WebSocket upgrades — do not remove it without proving the upgrade still works.
- Container-only: do not add a `target: local` install path.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
