# Solutions Interiors Website — Project Status

**Goal:** Finish/redesign the Solutions Interiors company website and publish it to represent the company well in the Egyptian and global market.

**Live working copy:** `site/index.html` in this repo (mirrored from the published Claude artifact: https://claude.ai/code/artifact/083fadb8-2d8c-4355-bae3-9d740d35a4cc — same URL across all rounds, version history preserved there).

## Company facts

- Solutions Interiors, est. 2013, Giza, Egypt.
- Manufactures/installs demountable partition systems, all engineered in-house: FineLine®, TechnoLine®, Fine Duo, Techno-Mix, Techno Advance, Trio, Flexi, Light-Line (8 systems) — plus in-house Aluminum Framed Door Systems (single/double glass or wooden), cladding, wooden works, HPL partitions, aluminum works, and doors.
- Also: Doors – Frame Series (leaf-frame door systems) and Glass Handrails.
- **Correction (Sep 2026):** the SOCAB (France) joint-venture/partnership is no longer accurate — all SOCAB references have been removed from the site.
- Current contact info (from live site footer): 27, Mariotya St., Al Haram, Giza, Egypt · info@solutionsinteriors.com · +202 3388 6297 · +20 102 289 3335 · +20 100 539 8787.
- Brand colors: accent orange `#f2701a` (light) / `#ff8a3d` (dark); near-black ink `#1a1a1a` / charcoal panel `#1c1b19`; off-white bg `#faf9f7`.
- Each product with a "Learn More" has a real PDF catalogue rasterized into a page-flip flipbook viewer.
- Real project history sourced from per-project `DATA.docx` files inside `WEB SITE PROJECTS -SELECTED PHOTOS & DATA\DONE\<PROJECT>\DATA.docx`, cross-checked against `projects reference list 2025.pdf`. Authoritative source for any future project additions.

## Domain / hosting / email infrastructure (confirmed Sep 2026)

- Domain `solutionsinteriors.com` is registered at GoDaddy, but DNS is managed through **Hostinger's DNS Zone Editor** (hPanel → Domains → DNS). This is the live, authoritative DNS (confirmed via the real site's CNAME/ALIAS records pointing to `*.cdn.hstgr.net`). Any future DNS changes (SPF/DKIM/DMARC, verification records, etc.) go here, not GoDaddy.
- Email is Hostinger Titan Business for `@solutionsinteriors.com` (mailboxes: `hossam@`, `info@`, `ola@`, `m.magdy@`, `noha@`, catch-all `all@`).
- **Fixed Sep 13 2026:** Titan's Email Reputation was "poor" due to a missing DKIM record. Added the DKIM TXT record (`titan1._domainkey`, RSA key) — status flipped to VERIFIED within ~1 minute. Also added a DMARC TXT record (`_dmarc`, `v=DMARC1; p=none; rua=mailto:info@solutionsinteriors.com`) — `p=none` is monitor-only, can tighten later. SPF already existed (`v=spf1 include:spf.titan.email ~all`).
  - If DKIM is ever regenerated in Titan, the new key must be re-copied into the same Hostinger DNS Zone Editor.
- **Google Analytics 4** set up (account "Solutions Interiors", property "Solutions Interiors Website", Measurement ID `G-FHEG85YCX5`) under Hossam's dedicated business Google account, linked to the already-active Google Search Console property (`sc-domain:solutionsinteriors.com`). GA4 wizard: both data-sharing options enabled, currency EGP, industry "Business & Industrial", business size "Medium".

## Status as of Sep 2026 — Round 2 feedback complete

1. Header/footer: removed redundant "Solutions Interiors" text span next to the logo (name is already in the logo art); logo enlarged (54px header, 44px footer).
2. Hero slideshow rebuilt as fully interactive: dots light up to show current photo (clickable to jump), plus prev/next arrows — wired through `showHeroSlide()`/`restartHeroTimer()`, still auto-advances every 5s and resets on manual interaction.
3. **Major fix** — PDF flipbook page-turn geometry: each catalogue page is a two-page spread (left + right physical page). Rebuilt from scratch with a proper 2-window book model, verified across all 10 systems (8 partition systems + Frame Series + Glass Handrails), forward/backward navigation confirmed correct.
4. Projects: all 18 featured projects' popup galleries expanded to 4-5 real photos each, all clickable into a full-screen lightbox (prev/next arrows, keyboard, photo counter).
5. Added "Doors – Frame Series" catalogue + flipbook (tagline: "Designed for Performance, Framed for Elegance").
6. Added "Glass Handrails" product under Aluminum Works, own catalogue flipbook (tagline: "The Modern Line of Sight"). Sourced only from the user's finished "PDF Lite Versions" brochures — not from competitor/vendor R&D reference folders (BTS Aluminyum, MetalGlas, Dawlia21, Alumil, etc.), which were correctly avoided.
7. Image budget: catalogue crops recompressed to 580px/q56 (~4.55MB, 106 images), project photos to 760px/q53 (~3.2MB, 86 photos). Final published HTML ~14.1MB, under the 16MB artifact cap.
8. Verified via local Playwright screenshots: header/logo, hero interactivity, flipbook page-turns, new product cards, project popup + lightbox, Arabic RTL layout.
9. Photo repair pipeline documented in `docs/PHOTO_REPAIR_NOTES.md`.

## Still open / blocked

- **Client logos** for Banque Misr, EBank, Al Baraka Bank, Apache: candidate sources found (Banque Misr official asset, Al Baraka via Wikipedia/seeklogo, Apache via companieslogo.com, EBank lower-confidence via mubasher.info). The Claude sandbox's network egress only allows a small package-registry allowlist — direct downloads to these hosts return 403. **Next step:** Hossam downloads/attaches the 4 logo files so they can be embedded and republished.
- Social links (Facebook/Instagram/LinkedIn/YouTube) still placeholder `#` hrefs.
- Footer legal links (Term of Use / Privacy Policy / Cookie Policy / FAQ) still inert `#` links.
- Contact form has no backend (mailto: only).
- Point the real `solutionsinteriors.com` domain at production hosting — the Claude artifact link is not a production deployment.
- DMARC policy is `p=none` (monitor-only) — revisit once reports show legitimate mail isn't flagged, then consider tightening to `p=quarantine`/`p=reject`.
