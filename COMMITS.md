# Commit Summaries

One entry per commit, newest first.

## Apply non-breaking npm audit fixes — 2026-10-27

- Ran `npm audit fix` (no `--force`): only `package-lock.json` changed; 16 indirect dependency updates (e.g. `shell-quote`, `undici`, `axios`, `js-yaml`).
- Vulnerabilities: 33 → 17 (2 critical, 15 high).
- Remaining fixes all need major upgrades and are deferred: `next` 14→16, `electron` 37→45, `electron-builder` 25→26, `adm-zip` 0.5→0.6, `postcss` (via Next).
- Verified: `npm run build:next` succeeds.
