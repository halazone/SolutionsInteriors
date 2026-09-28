# Decisions & open questions

Tracks the standing decisions and unresolved choices for this project, so they
don't get re-litigated or lost between sessions.

## Hosting

- **Settled:** hosting is already purchased — Hostinger, WordPress Starter plan.

## Final platform: static-site deployment (settled)

- **Settled.** Considered and rejected both alternatives:
  1. ~~WordPress + Elementor~~ — rejected by the user ("forget about elementor, i
     don't want to integrate into wordpress").
  2. ~~Simple custom CMS on Hostinger~~ — the user's original ask, but when we
     dug into the actual goal (keep every feature exactly as built in the
     artifact, and have Claude keep editing it directly after launch), a custom
     CMS was the wrong tool for that: it would mean rebuilding the site's markup
     to fit a database/admin-panel model, adding a maintenance burden, and making
     Claude's edits *slower* (through a CMS admin UI) rather than faster. The
     user agreed to drop it once this was explained.
- **What we're doing instead:** deploy the exact same code that's in this repo
  (`src/` + `assets/`) as a plain static site — real files on Hostinger's file
  manager, no database, no CMS, no page builder. This is the smallest possible
  gap between "what's in the artifact" and "what's live": same HTML/CSS/JS,
  same flipbook and language-toggle logic, 100% feature fidelity, because it's
  not a rebuild — it's the same file, just un-embedding the images from base64
  into a normal `/assets/` folder so they load as separate files instead of one
  giant inlined page.
- Editing going forward: Claude keeps editing the HTML/CSS/JS source directly
  (same as now) and re-uploads the changed files via Hostinger's file manager
  through a connected browser — the user logs into hPanel himself, Claude never
  handles the hosting password. This is faster per change than a CMS admin UI or
  Elementor, since it's a direct text edit, not a UI-driven page builder.

## Source control

- **Settled:** this GitHub repo (https://github.com/halazone/SolutionsInteriors)
  is the source of record from here on. The user is new to git/GitHub, so Claude
  handles all commits/pushes.
- Every round of feedback is committed with a clear message, and logged in
  `docs/CHANGELOG.md`.

## Backup plan for the current live site (when going live with the rebuild)

Agreed approach, to be executed as the first step before any changes to the live
domain:
1. Full backup (database + files) of the current live site via the host's backup
   tool or a plugin (e.g. UpdraftPlus) — the real safety net, kept as a downloadable
   archive.
2. Clone the current live site onto a password-protected subdomain (e.g.
   `old.solutionsinteriors.com`), not indexed by search engines (`noindex` +
   robots.txt disallow), so the existing design stays browsable for
   reference/comparison but visible only to the user and Claude (HTTP basic-auth
   password on the subdomain, credentials known only to the user — Claude verifies
   the lock is in place via a connected browser without ever holding the password
   itself). Subdomains are free on standard Hostinger plans (not a separate domain
   purchase) — only a single-site-restricted hosting tier would make this cost
   extra, which is not the case here (WordPress Starter allows this).
3. Only then publish the static-site rebuild to the live domain.

## Deployment workflow (settled, 2026-09-08)

From Round 4 onward: every change is built and uploaded to the **preview**
subdomain (`new.solutionsinteriors.com`) only. The **live** domain
(`solutionsinteriors.com`) is only touched when the user explicitly says to
publish/go live — never automatically as part of a normal round of edits.

## Image budget in the artifact-preview format

The published Claude Artifact (used for fast iteration during feedback rounds) has
a hard 16MB page-size cap, because every image is embedded inline as base64. This
is why project/product photos are compressed harder than ideal in `assets/optimized/`
right now. It is **not** a constraint of the final website — once live on real
hosting, images upload as normal separate files with no shared size budget, so
full-resolution originals can be used there. No urgency to move hosting early just
to fix this; it resolves itself whenever the move happens.

## Photo quality pass for the static-site launch

User requirement: photos should look more polished than the current compressed
artifact preview, but must **not** look "AI generated or unreal."

Audit of the 178 original source photos in
`WEB SITE PROJECTS -SELECTED PHOTOS & DATA/` (2026-09-08): median size is
4000×3000 — these are real camera/phone photos, not low-res to begin with. The
pixelation visible in the artifact today is a side-effect of the compression
used to fit the 16MB artifact cap (660px width, quality 45), not a source-image
problem. Only 26 of 178 (~15%) are under 800px on the short side, and those are
almost all Facebook-downloaded Nestle project photos (~720×960) plus a handful
of WhatsApp-compressed shots — genuinely soft at the source.

**Decision:**
1. For the ~85% with high-res sources: re-export at a much larger size/quality
   (target ~1600–2000px width, quality ~82–85, still web-optimized) once the
   16MB cap no longer applies on real hosting. This is a compression-setting
   change only — no upscaling, no AI — and resolves nearly all the pixelation.
2. For the ~15% that are genuinely low-res at the source (mostly the Nestle
   set): conventional (non-generative) upscaling only — Lanczos resampling with
   light, conservative sharpening. Explicitly ruling out generative/AI
   super-resolution tools (e.g. ESRGAN-style or diffusion upscalers), since
   those invent detail that wasn't in the original photo and are exactly what
   can look "AI generated or unreal." These photos will end up better than
   today's 660px versions but still visibly softer than the rest of the
   gallery — that's an honest ceiling of the source material, not something to
   paper over with generative fill.
3. Client logos and product thumbnails were also audited and are high-resolution
   at the source — no action needed there.
