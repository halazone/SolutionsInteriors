# Solutions Interiors Website

Source control for the Solutions Interiors company website rebuild (https://www.solutionsinteriors.com/), est. 2013, Giza, Egypt.

## Repo layout

- `site/index.html` — the current website, as one self-contained HTML file (mirrors the live published version).
- `docs/PROJECT_STATUS.md` — company facts, hosting/domain/email setup, and a full status log of what's done and what's still open.
- `CHANGELOG.md` — running history of every change, feature, and fix, newest first. Updated every time the site changes.

## Workflow

This repo is the project's system of record going forward. Every round of edits to the site gets:

1. An updated `site/index.html`.
2. A new entry at the top of `CHANGELOG.md` describing what changed and why.
3. An update to `docs/PROJECT_STATUS.md` if it affects open items, infrastructure, or company facts.
