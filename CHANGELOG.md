# Changelog

All notable changes to the Solutions Interiors website are logged here, newest first. This is the running history of the project — every feature, fix, or content addition gets an entry.

## 2026-09-28 — Repo set up as source control

- Created this GitHub repository as the project's source control, per Hossam's request.
- Imported the current live website (`site/index.html`) from the published Claude artifact (round 2 feedback complete — see `docs/PROJECT_STATUS.md` for full detail).
- Added `docs/PROJECT_STATUS.md`: company facts, hosting/domain/email infrastructure, and a full log of what's done and what's still open, reconstructed from the project history.
- Added this changelog to track future work.

## Earlier work (reconstructed, pre-dates this repo)

- **Round 2 feedback complete** (Sep 2026): header/logo cleanup, interactive hero slideshow, rebuilt PDF flipbook page-turn geometry, expanded project galleries + lightbox, added "Doors – Frame Series" and "Glass Handrails" products with catalogues, image compression pass to stay under the 16MB artifact cap, Arabic RTL verification.
- **Round 1 feedback**: removed all SOCAB (France) joint-venture references — no longer an accurate partnership.
- **DNS/Email (Sep 13 2026)**: fixed Titan email reputation by adding a DKIM record; added a DMARC record (`p=none`, monitor-only); confirmed SPF already in place. Confirmed Hostinger's DNS Zone Editor (not GoDaddy) is the live, authoritative DNS for the domain.
- **Analytics**: set up Google Analytics 4, linked to the existing Google Search Console property.
