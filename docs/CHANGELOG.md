# Changelog

All notable changes to the Solutions Interiors website project, in order. Each
round corresponds to a batch of feedback reviewed together and published as one
update to the live artifact preview.

## Round 8 — Functional contact form, bilingual SEO, mobile audit, security hardening

- **Made the contact form actually send email.** Wired it to Web3Forms (free
  tier, 250 submissions/month) via `fetch()` to `api.web3forms.com/submit` —
  no visual change to the form. Delivers to info@solutionsinteriors.com;
  the user is setting up a mailbox-level "Outgoing Forwarding" rule in
  Titan webmail so a copy also reaches sales@ (Web3Forms' free tier only
  supports one recipient). Added a bilingual sending/success/error status
  message under the Submit button, a honeypot field against spam bots, and
  a disabled-while-sending button state. Live-tested end to end on the
  staging site with a real submission.
- **Built full bilingual SEO** so the site can actually be found in search,
  in Arabic or English. The single-page EN/AR toggle only ever showed one
  language to a crawler (whichever localStorage happened to hold, which
  crawlers never carry), so `tools/build_static.py` now renders two real,
  independently-crawlable pages from one source — `/` (English default)
  and `/ar/` (Arabic default) — each with its own `<title>`, meta
  description, canonical link, and 3-way `hreflang` alternates (en / ar /
  x-default). Added JSON-LD structured data (`HomeAndConstructionBusiness`
  schema: name, Arabic alternate name, both addresses, phones, email,
  founding date, product offers), Open Graph + Twitter Card tags with a
  custom-composited 1200×630 share image, a full favicon set, and a
  `sitemap.xml` + `robots.txt`. Switched all built image paths from
  relative to absolute (`/assets/img/...`) so they resolve correctly from
  both `/` and `/ar/`. Verified live: correct `lang`/`dir`/title/H1 on
  each URL, correct OG/Twitter tags, sitemap and robots.txt reachable.
- **Mobile audit.** Tested the full site at a 375×812 mobile viewport:
  header/hamburger menu (opens, links scroll to the right section and the
  menu closes correctly), hero, Products (flipbook catalogue viewer works
  via tap), Projects (card → modal → photo lightbox, all functional),
  Contact form, and footer. Found and fixed one real bug: the "Why Choose
  Us" flip cards (Creative / Smart Solutions / Experienced / Support) only
  flipped on CSS `:hover`, so their back-face text was never reachable on
  a touch device — added a tap-to-flip toggle (`is-flipped` class) that
  works alongside the existing hover behavior on desktop.
- **Security / content-protection hardening**, per request: added a
  `.htaccess` (force HTTPS, `X-Content-Type-Options`, `X-Frame-Options`,
  `Referrer-Policy`, `Permissions-Policy`, directory-listing disabled,
  hidden-file access blocked, hotlink protection for images that still
  allows same-site use, search engine crawlers and social link-preview
  bots so SEO/OG previews keep working); disabled right-click and
  drag-and-drop on `<img>` elements and set `user-select:none` /
  `-webkit-touch-callout:none` on images site-wide. These are deterrents
  against casual copying, not real DRM — anyone determined can still
  save an image via browser dev tools or a screenshot, and this was
  communicated to the user rather than oversold.
- **Password-protected `new.solutionsinteriors.com`** via Hostinger's
  native "Password Protect Directories" feature (root `/`, HTTP Basic
  Auth) so the staging subdomain is private while updates continue there
  ahead of the production go-live.
- Uploaded the rebuilt `index.html` + `ar/index.html` + `.htaccess` to
  `new_site_preview/` (verified byte-for-byte against the local build via
  the file manager's resource-metadata API) and purged both the origin
  and HCDN edge caches.

## Deployment prep — static-site export + old-site backup

- Decided against both WordPress/Elementor and a custom CMS; settled on
  deploying the exact artifact code as a plain static site on the existing
  Hostinger hosting. See `docs/decisions.md`.
- Audited all 178 original project photos: pixelation in the artifact preview
  was almost entirely a compression-budget artifact (16MB cap), not a source
  problem. Added `tools/curate_projects_hires.py` / `assets/optimized_hires/`:
  re-exports the 121 curated project photos at 1800px/quality 85 (was
  720px/quality 50), with a conservative, non-generative upscale (Lanczos +
  light sharpening only, no AI super-resolution) for the 24 photos that were
  genuinely low-res at the source.
- Added `tools/build_static.py` / `dist_static/`: builds the same site as a
  real static file — images copied out to `assets/img/` instead of embedded
  as base64, wrapped in a proper standalone `<!doctype html>` document (the
  artifact host injects its own charset meta; a real hosted file needs one
  explicitly — without it, non-ASCII characters render as mojibake). Verified
  visually with Playwright: hero, projects grid, and project-modal galleries
  all render correctly with the higher-resolution photos.
- On Hostinger: created a manual full backup (files + database); created the
  `old.solutionsinteriors.com` subdomain and used Hostinger's "Copy Website"
  tool to clone the current live WordPress site into it; password-protected
  that subdomain via Directory Password Protection (confirmed returning 401
  without credentials) so it's a private, non-indexed reference copy of the
  current live site before any live-domain changes happen.

## Round 1 — Initial build

Rebuilt the site from scratch from the company's real WordPress export (content,
photos, and product catalogue PDFs), as a single self-contained bilingual (EN/AR)
HTML page: hero, about, process, product catalogue (with PDF-flipbook viewers for
downloadable catalogues), a projects gallery, client logos, and a contact section.
Established the build pipeline (`tools/build.py`) that concatenates page fragments
and embeds every image as a base64 data URI.

## Round 2 — Feedback pass

- Removed the redundant "Solutions Interiors" wordmark next to the header/footer
  logo and enlarged the logo mark.
- Made the hero slideshow dots interactive (active-state highlighting, click to
  jump) and added prev/next arrow buttons.
- Fixed the PDF catalogue flipbook's page geometry: each spread is now cropped
  into true left/right physical-page halves and the flip animation rotates around
  the real center spine, replacing the old whole-spread rotation.
- Expanded each of the 18 project galleries from 2–3 to 4–5 real photos, and added
  a click-to-enlarge lightbox (prev/next, keyboard navigation, photo counter).
- Added a "Doors – Frame Series" catalogue flipbook and a new "Glass Handrails"
  product under Aluminum Works, each with its own flipbook.
- Sourced and processed 4 previously-missing client logos (Banque Misr, EBank,
  Al Baraka Bank, Apache) once the user supplied them directly — direct download
  from the web was not possible from the build sandbox.

## Round 3 — Feedback pass

- **Hero:** primary CTA now points to Products (was Projects); "Unique Products"
  stat counter updated to 22+.
- **Products section:** subheading updated to "22+ products across partitions,
  cladding, doors, and joinery…".
- **New products:** added Mirrors and Shower Cabins; renamed the HPL category to
  "HPL Partitions, Mirrors & Shower Cabins" with a reworked category slogan and new
  taglines for both added products. New thumbnails applied to Doors – Frame Series,
  Glass Handrails, Wooden Cladding, Mirrors, and Shower Cabins.
- **Category order:** Demountable Partitions → Doors → Cladding → Aluminum Works →
  Wooden Works → HPL Partitions/Mirrors/Shower Cabins (bathroom segment last).
- **Demountable Partition System cards reordered:** Fine Line, Fine Duo, Techno
  Line, Techno Advance, Techno-Mix, Flexi, Light-Line, Trio (Trio kept at the end —
  it wasn't in the requested list but wasn't flagged for removal either).
- **Flipbook bug fix:** pages flipped earlier could stay visually on top of pages
  flipped more recently, because each page's z-index was fixed at creation time
  instead of reflecting flip order. Fixed with a monotonically increasing z-index
  counter assigned on every forward flip, and the original z-index restored on
  flip-back.
- **Projects gallery overhaul:**
  - Curated up to 15 non-repetitive, non-similar photos per project (121 photos
    total across all 18 projects, selected from 176 candidate photos supplied by
    the user).
  - Moved the project description/meta block above the photo grid in the popup
    (was below).
  - Replaced the broken fixed main-shot/side-shots gallery layout (some photos
    zoomed in, others not displayed) with a proper responsive grid.
  - Unified the "Partitions" scope label to "Demountable Partitions" across all
    projects.
- **Client logos:** processed and added 18 new client logos (background removed
  via flood-fill, fitted to the existing 320×87 transparent-PNG convention used by
  the original 7 logos) to the carousel.
- **Contact section:**
  - Office address is now a clickable Google Maps link.
  - Added a Factory address (Abou Rawash Industrial Zone) with its own Maps link.
  - Added a second email address (sales@).
  - Replaced all phone numbers with the current set.
  - Social row reduced to Facebook only, linked to the real page.
- **Footer:** swapped the horizontal wordmark logo for a stacked (icon-over-name)
  version, enlarged; removed the About Us / Terms of Use / Privacy Policy / Cookie
  Policy / FAQ links (pages that don't exist yet).

Image budget note: with 121 curated project photos plus everything else, the
published artifact sits at ~15.2MB against the platform's 16MB cap — there isn't
much headroom left for further photo additions in the artifact-preview format
without trimming something else first (this ceiling does not apply once the site
is on real hosting).

## Round 4 — Feedback pass

From this round onward, every change is built and uploaded to the **preview**
subdomain (`new.solutionsinteriors.com`) only; the live domain
(`solutionsinteriors.com`) is only touched when explicitly told to publish. See
`docs/decisions.md`.

- **Product cards:** reordered so Trio System comes before Flexi System.
- **Product cards are now clickable**, opening a full-size popup (name,
  description, photo at its true aspect ratio) with a FLIP animation — the
  popup visually grows from the clicked card and shrinks back into it on
  close. The catalogued products' "Learn More →" button moved into this popup
  (was directly on the card); clicking it closes the popup and opens the PDF
  flipbook for that product.
- **Projects gallery re-curated:** every project now has up to 15 photos
  (previously capped lower), auto-selected and ordered best-first by a
  non-generative quality score (sharpness via Laplacian variance, contrast,
  and exposure balance) with near-duplicate photos filtered out. Photos get a
  conservative auto-contrast/contrast/color pass (not AI upscaling). The
  gallery grid is now a Pinterest-style "justified" layout: photos keep their
  original aspect ratio (no more forced square/4:3 crops) and are packed edge
  to edge into rows, à la Google Photos/Flickr.
- **Removed** the "National Bank of Egypt – Al Gezira Plaza" project (photos
  weren't well represented).
- **Client logos:**
  - Fixed a background-removal bug where enclosed "holes" in a logo (e.g.
    inside a letter "O", or a closed shape) stayed solid near-white instead of
    transparent, which showed up as ugly white blobs in dark mode. Reprocessed
    16 of the 25 logos affected.
  - Slowed the carousel rotation (34s → 68s per loop) and fixed the seamless
    loop: the track content is now duplicated once in the HTML (not rebuilt at
    runtime), and the animation stays paused until every logo image has
    actually loaded, which also fixes the "blank for a moment, then logos pop
    in" flash previously seen in Arabic mode.
- **Catalogue PDFs upscaled:** discovered via `pdfimages -list` that the
  source PDFs' embedded images are natively higher-resolution than the
  ~70 DPI previously used for the flipbook page images; re-rendered all 10
  catalogues at 120 DPI (real decoded detail, not AI upscaling) with light
  unsharp-masking.
- **Flipbook page-turn glitch fixed:** rapid clicking of next/previous could
  overlap two page-flip animations and leave a page stacked visually on top
  of another mid-turn. Fixed with an animation lock that ignores further
  next/prev clicks until the current flip (700ms) finishes.
- **Hero slideshow captions added:** each of the 10 hero photos now shows its
  project name and system used in a small, unobtrusive corner label.

New tooling added this round: `tools/select_project_photos.py` (photo
quality-scoring + de-dup curation), `tools/fix_logo_transparency.py` (enclosed
transparency-hole repair), `tools/render_catalogue_hires.py` (native-resolution
PDF catalogue re-render).

## Round 4b — Feedback pass

- **Pixelated product thumbnails fixed:** the 8 core Demountable Partition
  System thumbnails (Fine Line, Fine Duo, Techno-Line, Techno Advance, Techno
  Mix, Trio, Flexi, Light-Line) were low-resolution crops pulled from the old
  WordPress site. Found that the same photography already exists at native
  resolution inside each system's own PDF catalogue (already hi-res-rendered
  in Round 4); cropped clean, text-free regions straight from those pages
  instead of upscaling, for a real quality gain rather than an artificial
  one. The other 7 thumbnails with no better source available (Wooden Doors,
  Glass Doors, Glass Cladding/Curtain Walls/Pergolas, Aluminum Windows,
  Closets, Kitchens, HPL Partitions) got the project's standard conservative
  upscale (2x Lanczos + light unsharp-masking, no generative/AI upscaling).
- **Flipbook backward-flip glitch fixed:** flipping to the previous page
  briefly showed the un-flipping page on top of the rest of the left-hand
  pile mid-turn. The page's z-index was being reset to its resting value at
  the start of the flip instead of after the 700ms turn animation finished;
  now it stays elevated for the full turn.
- **Product popup backdrop fade synced with the open/close animation:** the
  darkened backdrop behind the product popup, flipbook, project, and
  lightbox modals used to snap instantly from dark to fully transparent (or
  back) instead of fading, because it was toggled with `display:none/flex`
  (which CSS transitions can't animate). Switched all four modals to an
  opacity/visibility toggle with a proper `.32s` fade, and made the product
  popup's close start that fade immediately rather than waiting for the
  popup box's own shrink animation to finish.

Verified all three fixes with a Playwright pass against the rebuilt static
site (thumbnail sharpness, forward/backward flips, popup open/close) before
upload.

## Round 5 — Learn More bug + missing project photos

- **Learn More button showing on every product, not just catalogued ones:**
  `.btn{display:inline-flex}` and the browser's default `[hidden]{display:none}`
  rule are both specificity `(0,1,0)`, and author styles always win a tie
  against the built-in UA stylesheet — so setting `productModalLearnBtn.hidden
  = true` in JS for non-catalogued products had no visible effect. Fixed with
  an explicit `.product-modal-learn[hidden]{display:none}` override. Verified
  live: `hidden` products (e.g. Wooden Doors) now compute `display:none`;
  catalogued products (e.g. Fine Line System) still show the button.
- **Missing project photos in the popup gallery, and a regression during the
  fix:** root-caused to an incomplete original upload to Hostinger — every
  project except Amazon was missing photo #2 onward from `assets/img/`
  (nothing wrong in the repo or build; `tools/build_static.py` resolved every
  file correctly from the local `assets/optimized_hires/` source). A first
  delta-zip fix was sent for manual upload/extraction, but only one of two
  parts got extracted and the extraction wiped the pre-existing files
  instead of merging with them, so even Amazon's original photo #1s were
  lost. To stop this recurring, switched the whole delivery process to a
  self-managed upload: authenticated directly against Hostinger's File
  Browser (filebrowser.org) REST API to diagnose exactly what existed on the
  server, then, once the missing set was known (97 files), used the Chrome
  extension's file-input upload tool to push every missing photo plus the
  corrected `index.html` straight into `new_site_preview/` in the browser —
  no manual zip/extract step for the user at all. Verified after upload: all
  148 expected `p3_*` project photos return 200 on the live domain, the CSS
  fix is present in the live `index.html`, and the UGDC gallery (previously
  missing several photos) now renders in full.

## Round 5b — Missing catalogues + footer logo alignment

- **Six product catalogues showing a blank/broken flipbook (Techno Line,
  Techno Advance, Trio, Flexi, Light-Line, Glass Handrails):** same root
  cause as the Round 5 project photos — an incomplete original upload, this
  time of the flipbook spread images (`technoline-*.jpg`, `tadvance-*.jpg`,
  `trio-*.jpg`, `flexi-*.jpg`, `lightline-*.jpg`, `handrails-*.jpg`). Of the
  106 catalogue spread images the 10 flipbooks need, 61 were missing on the
  server (all local/repo copies were present and correct). Diagnosed via
  the same live HEAD-check sweep used in Round 5, then uploaded all 61
  missing files directly into `assets/img/` the same self-managed way.
- **Footer logo enlarged and aligned:** the icon (grid mark + orange square
  + "S") sat noticeably narrower than the "SOLUTIONS"/"INTERIORS" wordmark
  below it. Rebuilt `logo_stacked_black.png`/`logo_stacked_white.png` by
  cropping the icon from the higher-resolution horizontal header logo
  (511x165, vs. the stacked composite's original 218x201) instead of
  upscaling the low-res version, scaling it so its left/right edges land
  exactly on the text block's edges, and re-compositing it above the
  existing text layer. Verified locally with Playwright and confirmed live
  on the public site and in the Claude Artifact preview.

## Round 6 — 90 missing images (header logo, hero, catalogues) + cache-busting

- **Reported bug turned out to be two separate things.** The user saw the
  old small footer logo but all photos loading on their main PC's browser,
  and the new footer logo but many broken photos on a fresh profile/device.
  Root cause of *that specific symptom*: ordinary HTTP caching — every image
  response carries `Cache-Control: public, max-age=604800` (7 days) while
  the HTML document itself is never cached, so a browser with a week-old
  local cache keeps serving stale image bytes (old logo) after an update,
  while a cache-free browser fetches everything fresh. Hostinger's own edge
  CDN (HCDN) caches independently per edge node on top of that. Cleared
  both layers (`.../vhosts/solutionsinteriors.com/cache/clear` and
  `.../cdn/vhosts/new.solutionsinteriors.com/purge`, both via the same
  hPanel API used in Round 5) to resolve the immediate symptom.
- **That didn't explain everything — a full re-audit found 90 more missing
  files, most never previously flagged.** Following through on "these are
  examples, not all what's bugged," extracted the ground-truth list of all
  316 image files the live page actually references straight from the
  built `index.html` and HEAD-checked every one against the live server.
  90 were genuine 404s (not cache staleness) — all present and correct in
  the local repo, never uploaded: the header/footer logo itself
  (`Logo-Black.png`, `Logo-White.png`), all 10 hero slideshow images, 3
  About-section stock photos, the entire client-logo strip (7 named brand
  logos + 18 `client_*.png`), 5 product-card thumbnails, and — the biggest
  chunk — 4 whole catalogue flipbooks that had never been uploaded at all
  (Fine Line, Fine Duo, Frame Series, T-Mix: 42 spread images combined)
  plus 3 residual Trio spreads missed by Round 5b's catalogue fix. Uploaded
  all 90 directly into `new_site_preview/assets/img/` the same self-managed
  way as prior rounds (this time via the Chrome extension's file-input tool
  in 4 batches, since each upload call is capped at 10MB). Re-ran the full
  316-file sweep after uploading and after a second cache purge: 0
  failures.
- **Cache-busting so this class of bug can't recur silently.** The root
  problem behind the *original* report — a returning visitor's browser
  serving week-old cached bytes after a real update — had no fix before
  this round beyond manually clearing Hostinger's caches each time.
  `tools/build_static.py` now appends `?v=<8-char content hash>` to every
  image URL it emits (computed from the file's own bytes, so unrelated
  images keep their existing cache-busted URL and stay served from cache).
  When an image's content changes in a future round, its hash — and so its
  URL — changes too, which every browser (including one with a week-old
  cache of the old URL) treats as a brand-new resource and fetches fresh,
  with no manual CDN/browser cache purge required going forward. Verified
  the live `index.html` now serves all 316 image references with a `?v=`
  tag and that the site renders correctly (header/footer logo, hero
  slider, About section, catalogues) end to end.

## Round 7 — New thumbnails, product catalog restructure, project covers + 7 new projects

- **Replaced 13 existing product thumbnails with the higher-quality versions
  supplied in the shared folder** (Fine Line, Fine Duo, Techno Line,
  T-Advance, Techno-Mix, Trio, Flexi, Light Line, Frame Series, Wooden
  Cladding, Handrails, Mirrors, Shower Cabins), matching prior dimensions.
  Brightened the Glass Cladding thumbnail specifically (`Brightness×1.35,
  Contrast×1.08, Color×1.05`) before upload — the supplied source photo read
  noticeably dark next to the other cards.
- **Restructured the Products section** from 22 to 17 cards across 5
  categories: removed Curtain Walls, the entire Wooden Works category
  (Closets, Kitchens, Pergolas), and HPL Partitions; moved Glass Handrails
  out of Aluminum Works into the final category alongside Mirrors and Shower
  Cabins, renamed that category from "HPL Partitions, Mirrors & Shower
  Cabins" to **Glass Works** with a new slogan reflecting glass detailing
  (handrails, mirrors, shower enclosures). Reverted the "22+" product count
  back to **17+** in both the home stats counter and the Products section
  description, matching the new true count.
- **Added a project cover-photo field to the template.** Each project now
  has a dedicated `cover` image: shown as the card thumbnail in the grid,
  and — new — as a large hero image at the top of the project modal, above
  the scope/products/year/location boxes, with the numbered photo gallery
  below that (previously the modal had no separate cover — it opened
  straight into the description boxes). Verified with the live Amazon and
  Squash Building modals.
- **Added 7 new projects** (ACT, Apache, Knowledge City, Maxim, NBE
  Sheraton, Squash Building, World Bank) from the supplied project folders
  and description files, following the existing template and field
  structure. Two of the new projects' source files had no stated location:
  used "Cairo" for Maxim and "Sheraton, Cairo" for NBE Sheraton as
  reasonable fallbacks. Four new projects (Apache, Knowledge City, Maxim,
  World Bank) had a supplied cover photo that was an exact duplicate
  (MD5-matched) of one of their gallery photos; the duplicate was dropped
  from the numbered gallery in each case to avoid showing the same photo
  twice, slightly reducing their photo counts (15→14, 15→14, 15→14, 10→9
  respectively). One file in the covers folder, "Madrasetna Cover.jpg", did
  not correspond to any of the 7 new projects or the 17 existing ones (a
  different, unrelated branded office space) and was left unused.
- **Re-ordered all 24 projects by descending photo count** (most photos
  first), from 15 down to 4: Amazon, NBE Shereif, Al Ain Channel, UGDC (15
  each) → Apache, Knowledge City, Maxim (14 each) → Ain El-Sokhna Harbor
  (13) → Bavaria (10) → World Bank (9) → L'Oréal, Ahly Bank Baron, NBE
  Sheraton (8 each) → Sharm, Squash Building (7 each) → Uber, Port Said,
  GAFI (6 each) → Nestlé, JLL, Kone, Ebank 5th Settlement, ACT (5 each) →
  Intel (4).
- **Applied the requested description-text edits**: Amazon's scope box now
  includes "Doors - Frame Series" and its products box now includes
  "ElegantFrame® single & double glazed"; the products box wording for NBE
  Shereif / Port Said / Ebank 5th Settlement changed from "... Profiles" to
  "... System" (NBE Shereif was already using "System" and needed no
  change).
- **Made the home hero slideshow caption clickable and more visible.**
  Restructured the caption data into project-name / system-name pairs, each
  independently clickable: clicking the project name scrolls to and opens
  that project's modal; clicking the system name scrolls to Products and
  opens that system's flipbook catalogue. Increased the caption's font
  size, contrast, and background opacity so it reads clearly over any hero
  photo, and gave both segments an underline + hover color to signal they
  are interactive. Verified both links functionally with Playwright.
- Uploaded all changed/new assets (112 images + the rebuilt `index.html`)
  to `new_site_preview/`; caught and fixed a batch-upload issue where the
  file manager's "Replace or skip files" confirmation dialog silently
  blocked overwrites of 13 already-existing filenames — re-uploaded those
  explicitly and confirmed the dialog before clicking "Replace all files in
  destination folder." Purged both the origin cache and the HCDN edge cache
  afterward, and did a final live check of new.solutionsinteriors.com
  confirming: 17 product cards, the "17" stat counter, no leftover
  references to the removed products, the Glass Works section, the
  clickable hero caption, and the Amazon project modal rendering with the
  new cover-photo-on-top layout and the updated scope/products text — all
  byte-for-byte matching the local build.

## Round 10 — Production go-live + Arabic terminology + contact icons
- **Production cutover**: solutionsinteriors.com now serves the new static site. Prior WordPress installation safely relocated (not deleted) to public_html/wordpress_root_backup/ on the server. Clean, non-password-protected .htaccess deployed to production.
- **Arabic terminology**: Shifted Arabic copy from generic MSA terms to professional Egyptian trade terminology for better local search relevance — قواطيع (partitions) instead of حوائط فاصلة, تجاليد/تجليد (cladding) instead of كسوات/كسوة — applied across hero copy, products section, all 23 project entries, section intros, page title, meta description, and JSON-LD structured data (now language-aware via structured_data(lang)). Meta description intentionally retains both terms for maximum keyword coverage.
- **Contact section icons**: Replaced the four emoji (📍🏭✉️☎️) next to Office/Factory/Email/Phone with custom minimal stroke-based SVG icons in the site's orange brand accent color, matching the existing minimal aesthetic.
- Rebuilt and redeployed to both staging (new_site_preview) and production; CDN caches purged for both domains; live-verified EN and AR pages, RTL rendering, and new icons on production.

## Round 11 — Mobile UX: swipeable rows + fixed mobile menu
- **Mobile menu bug fix**: the mobile hamburger dropdown was rendering as an unreadable ~50px sliver instead of a full-screen panel. Root cause: `header.site` had `backdrop-filter` directly on it, which (per spec, same as `transform`/`filter`) makes an element the containing block for any `position:fixed` descendant — and the mobile nav panel is `position:fixed` inside the header. Fixed by moving the blur/translucency onto a `header.site::before` pseudo-element instead, so header.site itself no longer hijacks the containing block and the nav panel's `inset` resolves against the real viewport again.
- **Swipeable card rows (phone view)**: product category rows and the 24-card projects grid now become horizontal snap-scrollers below 640px instead of one long stacked column — peek of the next card + hidden scrollbar + scroll-snap, plus a small dot indicator (≤8 items) or "n / total" counter (>8 items, e.g. projects) so it's obvious there's more to swipe. Deliberately NOT applied to the 4-item Process/Why-Choose-Us grids, which already fit 2-up on a phone and are more scannable as a static grid than as something requiring a swipe gesture. Desktop/tablet grid layouts are untouched (rule is scoped to a max-width:640px query with a `.swipeable` class add-on).

## Round 12 — "Catalogue" badge on product cards
- **Catalogue badge**: the 10 of 17 products that have a full flipbook catalogue (8 partition systems, Doors – Frame Series, Glass Handrails) now show a small dark "Catalogue" pill with a document icon in the top corner of their thumbnail — on both the desktop grid and the mobile swipeable cards — so visitors can see at a glance which products have a full catalogue instead of opening every product's popup to check. Purely presentational: driven by the same `data-flipbook` attribute already used to show/hide the popup's "Learn More" button, so no new data or JS logic was needed — just a CSS pseudo/positioned badge plus one small markup addition per catalogued card.
- Badge uses `inset-inline-end` so it automatically sits in the correct corner in Arabic/RTL without a separate override rule, and its label text goes through the existing `data-en`/`data-ar` translation system (nested in its own inner span so language-switching doesn't wipe out the icon).
- Rebuilt and redeployed to both staging (new_site_preview) and production (root + /ar/); CDN caches purged for both domains; live-verified on solutionsinteriors.com that exactly 10 of 17 product cards carry the badge and the product popup / "Learn More" flow is unaffected.

## Round 13 — SEO fixes (title/meta trim, Product→Service schema, lazy-load + WebP images)
- Prompted by a 60% score on a third-party checker (seobility.net) — since the actual report wasn't accessible, audited the live site directly and fixed the concrete, verifiable issues found.
- **Title/meta description length**: both EN and AR `<title>` and meta description tags were oversized enough to get truncated in Google search results. Trimmed EN title to 56 chars / description to 157 chars, AR title to 55 chars / description to 148 chars, while keeping the established Egyptian-Arabic trade terminology and key search terms.
- **Structured data fix**: `makesOffer.itemOffered` was typed as schema.org `Product`, which Search Console flagged as "5 invalid items" (Product rich results require price/offers/image, which these B2B service categories don't have a fixed public value for). Changed to `Service`, the semantically correct type — no data was fabricated to satisfy Product validation.
- **Lazy-loading + layout stability**: build script now adds `loading="lazy"` + `decoding="async"` + `width`/`height` to below-the-fold `<img>` tags automatically (header logo and JS-driven placeholder images like the lightbox/modal are excluded). The hero slideshow's background images (set via JS, not `<img>` tags) got a matching custom fix — only the first visible slide loads immediately, the other 9 defer via `requestIdleCallback`/`setTimeout` with a safety-net load if a slide is shown before the deferred batch finishes.
- **WebP conversion**: product/project JPGs are now converted to WebP at build time, but only kept when the WebP output measures smaller than the source (378 of 379 eligible files converted, ~51.5MB / ~45% off total image payload; 1 file kept as JPG because WebP came out larger).
- Rebuilt and verified locally (Playwright: hero slideshow, lazy-loaded product/cladding photos, no broken images). All 414 image files (assets/img) plus the rebuilt EN/AR `index.html` were uploaded to `new_site_preview/` (staging) in 7 batches via the file manager, replacing the 35 filenames that collide (logos, favicons, og_image) and adding the rest as new files since nearly every product/project photo's extension changed from `.jpg` to `.webp`. CDN cache purged for new.solutionsinteriors.com. **Production (solutionsinteriors.com) intentionally left untouched**, pending Hossam's review and approval.
- **Known issue found during verification, not caused by this round**: immediately after the upload, both preview subdomains — new.solutionsinteriors.com and old.solutionsinteriors.com — started returning `503 Service Unavailable`, including with the CDN bypassed (Development mode). Since old.solutionsinteriors.com was not touched at all this round and shows the identical error, while solutionsinteriors.com (production, also untouched) loads normally, this points to a Hostinger-side issue affecting both parked subdomains rather than anything in this upload. Flagged to Hossam to check with Hostinger / retry once resolved, since the staging content itself could not be live-verified as a result.
- Hossam confirmed by eye on staging that the site looks correct ("can't seem to find a visual difference") and approved moving Round 13 to production.

## Round 13b — Client-logo carousel: fixed logos not loading + white edge fringe in dark mode

- **Bug 1 — some logos only loaded after switching modes (Dar, Apache, etc.)**: Round 13's new site-wide `loading="lazy"` default was being applied to the 50 `<img>` tags in the client-logo marquee too. That carousel lays 25 unique + 25 duplicate logos out in a single very-wide `width:max-content` row, animated with a CSS `transform` (not real scrolling), so most of the logos sit far outside the browser's native lazy-load viewport threshold at first paint and their fetch got deferred — only re-triggered later by an unrelated style recalc (e.g. toggling dark mode), which is exactly the symptom reported. Fixed by adding `loading="eager"` explicitly to all 50 logo-track `<img>` tags in `src/body3.html`, which also keeps the build script from re-adding `loading="lazy"` (it skips any tag that already declares `loading=`). Verified with Playwright: 0 unloaded logo images in both light and dark mode, immediately on load, no mode switch needed.
- **Bug 2 — visible white line around some logos in dark mode (Banque Misr, EHAF, Al Shorouk, SNA, JLL, KONE, ECG, Hassan Allam, and others)**: the original background-removal script only flood-fills transparency in from the image's outside edges within a color tolerance, so anti-aliased boundary pixels that blend the logo's true edge color with white — just outside that tolerance — were left fully opaque. Invisible on the site's light background, but the dark-mode CSS filter (`grayscale(1) brightness(1.6)`) brightens that thin residue into an obvious halo hugging the logo outline. Wrote a new fix (`tools/fix_logo_edge_fringe.py`) that iteratively erodes near-white opaque pixels touching a transparent neighbor, a few passes deep, stripping the fringe layer-by-layer without touching solid interior logo color. Applied to the 16 most-affected logos, clearing 65–460 fringe pixels each (`client_dar.png` had none to begin with). Re-ran the existing enclosed-hole fixer (`tools/fix_logo_transparency.py`) afterward as a second pass — found no enclosed holes, confirming the edge fringe was the only remaining defect. Verified with before/after pixel comparisons and a live-page dark-mode screenshot check of every affected logo — no visible fringe remains on any of them.
- The ambiguous "SG" logo in Hossam's report turned out to be `client_el_soadaa.png` — El Soadaa Group's logo mark is a stylized "SG" monogram with "El Soadaa Group" in small text underneath, confirmed visually.
- Rebuilt (`tools/build_static.py`) and verified locally with Playwright: 0 broken/unloaded images anywhere on the page, all 50 logo-track images load immediately in both light and dark mode, no visible edge fringe on any client logo in dark mode.
- Deployed together with all of Round 13's SEO changes to **both** staging (`new_site_preview/`) and production (`public_html/` root), per Hossam's approval and explicit request to fix and ship to both. CDN caches purged for both `new.solutionsinteriors.com` and `solutionsinteriors.com`. Live-verified both domains after deploy.

## Round 13c — www → non-www 301 redirect (seobility.net re-check: 60% → 75%)

- Hossam re-ran seobility.net after Round 13/13b: score rose to 75%, with one remaining critical error: `www.solutionsinteriors.com` was serving the exact same content as `solutionsinteriors.com` with its own `200` — duplicate content, since every canonical tag/sitemap entry already points at the non-www host. Confirmed directly (network request to www. returned 200, no redirect) before fixing.
- Added a `www` → non-www rule to the generated `.htaccess` (`tools/build_static.py`), ordered so it combines with the existing `http` → `https` rule into a single redirect hop for every case (www+http, www+https, non-www+http all resolve in one 301 straight to `https://solutionsinteriors.com/...`).
- Deployed the updated `.htaccess` to production and verified live: `https://www.solutionsinteriors.com/` now 301s straight to `https://solutionsinteriors.com/`. Not pushed to staging's `.htaccess` — `new.solutionsinteriors.com` has no `www.` variant so the rule is a no-op there.
- **The other item on Seobility's to-do list ("use good alternative descriptions for images") is a false positive, not a bug**: audited every `<img>` in the built page — the only `alt=""` tags are the 25 `aria-hidden="true"` duplicate logos in the marquee's seamless-loop second copy (correctly empty per a11y best practice, since they're decorative and hidden from screen readers) and 3 JS-driven placeholders with no `src` yet (lightbox/modal images, filled in on click). Every real, visible image already has a descriptive `alt`. Left as-is.
- Not investigated this round, need more than Seobility's summary view to act on: "Page structure" (62%) and "External factors" (30%, largely backlinks/social signals — outside what a code fix can move).

## Round 13d — Heading hierarchy fix (Page structure error: "H3 heading level is missing")

- Hossam followed up on Round 13c's deferred "Page structure" item by opening Seobility's "Elements" tab, which flagged: "H3, H3, H3 heading level is missing" — a heading-hierarchy skip somewhere in the page's 44 headings.
- Independently parsed the live `dist_static/index.html` for every `<h1>`–`<h6>` in document order and diffed each heading's level against the previous one. Found exactly 2 genuine skip points (H2 followed directly by H4, with no H3 in between), both in `src/body1.html`: the "From First Visit to Handover" process-step cards (Consultations, Solutions, Reviews, Delivery) and the "Quality is not just a standard, it's a commitment." / "Why Choose Us" flip cards (Creative, Smart Solutions, Experienced, Support) — 8 `<h4>` tags total. Seobility's own "Show all 44 headings" detail view hit its daily free-tier rate limit before it could be used to reconcile the exact reported count, but fixing both confirmed skips resolves the real underlying structural defect regardless.
- Changed all 8 `<h4>` tags to `<h3>` in `src/body1.html` (purely semantic — no text or attribute changes). Updated the two CSS selectors that targeted them by element type, `.step-card h4` and `.flip-front h4` in `src/index.template.html`, to `.step-card h3` / `.flip-front h3` so the visual font sizing stayed pixel-identical (font-size/margin values unchanged, just re-targeted to the new tag). Left `.prod-body h4` untouched — those product-card titles are already correctly nested under an H3 and were never part of the defect.
- Rebuilt and re-ran the heading-order script against the new `dist_static/index.html`: 44 total headings (unchanged), 0 skip points (down from 2). Visually verified via Playwright screenshots that the process-step cards and why-choose-us flip cards render at their original size and layout — no regression.
- Deployed the updated `index.html` and `ar/index.html` to both staging (`new_site_preview/`) and production (`public_html/` root), purged CDN cache for both domains, and live-verified via an in-page script on both `solutionsinteriors.com` and `new.solutionsinteriors.com`: 44 headings, 0 skips on both.

## Round 14 — Google Analytics 4 setup + Search Console link

- Hossam asked where to find visit/traffic numbers for the site. Hostinger's built-in "Analytics" panel turned out to be raw server-log/request stats, not real visitor analytics, and no analytics tool had ever been installed on the site (confirmed via source grep). Recommended GA4 + Google Search Console over Microsoft Clarity, given the site's actual business model: B2B lead generation (demountable partitions, doors, cladding for corporate/contractor clients), with the contact form as the conversion goal.
- Hossam asked me to set both up end-to-end. Created a new GA4 account ("Solutions Interiors") and property ("Solutions Interiors Website") from scratch under his dedicated business Google account (separate from his personal Gmail). Paused mid-wizard on request; Hossam then made his own choices directly in the browser — ticked on both "Modeling contributions & business insights" and "Recommendations for your business" data-sharing options, set currency to EGP, industry category to "Business & Industrial," business size to "Medium" — overriding my initial more-conservative defaults (unticked data-sharing, USD, Home & Garden). Reviewed and agreed with all four changes (low-risk aggregated data-sharing is a reasonable call for him to make; EGP/Business & Industrial/Medium are all a better fit for an Egypt-based B2B company than my initial guesses) and continued.
- Obtained the Measurement ID (`G-FHEG85YCX5`) and the standard Google tag (`gtag.js`) snippet. Wired it into `tools/build_static.py`'s `build_page()` so it's injected first in `<head>` (per Google's own install instructions) on every built page, both `/` (English) and `/ar/` (Arabic).
- Discovered Google Search Console was already fully set up and active — `sc-domain:solutionsinteriors.com` verified, sitemap submitted and successfully crawled since Round 13's SEO work — this wasn't previously documented. Only needed to link it to the new GA4 property (Admin → Product links → Search Console links), which was done successfully.
- Rebuilt both language versions, uploaded to both staging (`new_site_preview/`) and production (`public_html/` root), purged CDN cache for both domains, and live-verified the `gtag.js` script tag with the correct Measurement ID is loading on both `solutionsinteriors.com` and `new.solutionsinteriors.com`.
- **Deferred, not a blocker**: marking the site's `form_submit` event as a GA4 "key event" (conversion) for the "Generate leads" objective — GA4 only lets you star an event once it's actually appeared in Recent events, which needs at least one real contact-form submission (or general traffic) to land first. Revisit once the site has real visits/submissions.

## Round 14b — Git history reconciliation (repo access restored in a separate session)

- **What happened**: earlier attempts to push this repo's history to GitHub failed with a "not in this session's authorized repository set" proxy error (see Round 8+ commits, all made locally but never pushed). Hossam separately resolved GitHub access for this repo in a different conversation. That session had no access to this sandbox's local working copy (each Claude session runs in its own isolated container), so — not knowing 23 commits' worth of real history already existed locally — it reconstructed the project from context/memory and pushed a fresh 2-commit history to `origin/main`: a stub root `CHANGELOG.md`, a `docs/PROJECT_STATUS.md` summary (both reconstructed, and already stale — labeled "Round 2 feedback complete" versus the real Round 14), and a one-off static snapshot at `site/index.html`.
- **Nothing was actually lost** — the full real history (this file, `docs/decisions.md`, `src/`, `tools/`, `assets/`, all 23 rounds of commits) was sitting intact in this sandbox's local clone the whole time, just never pushed.
- **Reconciled** by merging `origin/main`'s 2 commits into local `main` (`--allow-unrelated-histories`), keeping this repo's real README (the reconstructed one duplicated its repo-layout section) and removing the 3 redundant/stale files it added (`CHANGELOG.md` at root, `docs/PROJECT_STATUS.md`, `site/index.html`) so there's a single source of truth: this file (`docs/CHANGELOG.md`) for history, `docs/decisions.md` for settled decisions, and `src/`/`tools/build_static.py` → `dist_static/` (gitignored, regenerate don't commit) for the actual site. Pushed the reconciled history to `origin/main` as the new authoritative state.
- **Lesson for future rounds**: this repo's local working copy lives in an ephemeral, session-specific sandbox — if a push ever fails, the fix is to resolve access and push *from that same session*, not to start fresh in a new one assuming the remote's current state is the full picture.
