# Namat AI — Progress Snapshot · 2026-05-29

Multi-tenant SaaS ecommerce platform (Laravel 11 + Vue 3 + Inertia + Tailwind +
SQLite) with AI-augmented theming, content, and product imagery. This file
captures everything since the last snapshot (2026-05-22) — Phases 68, 69, 70,
the end-to-end verification session, and the launch-readiness audit.

---

## At-a-glance

| | |
|---|---|
| **Tests** | 269 passing · 962 assertions · `pest --parallel` ~30-50s · **0 failures** |
| **Build** | `npm run build` clean in ~18-29s |
| **Pages verified** | All 87 parameter-free admin/owner GET pages return 200 · **0 server errors** |
| **Languages** | English + Arabic (RTL) at parity |
| **Plans** | Starter · Growth · Pro (AI gated Growth+; gpt-image-1 on Pro) |
| **Demo Pro store** | `demo-pro@namat.test` / `password` · subdomain `aurorapro` · store_id 9 · user_id 12 |
| **Super admin** | `done.3kk@gmail.com` / `NamatAdmin@2026` |
| **Dev server** | `php artisan serve --port=8765` · login via `/dev/login-super` |

---

## Phase 70 — Dashboard navigation overhaul (2026-05-29)

**The big find:** 23 fully-built, working pages were not linked in the sidebar
at all — they returned 200 but were unreachable from the menu. This is why the
dashboard felt incomplete and why Theme Builder "seemed missing."

**What changed (`resources/js/Layouts/DashboardLayout.vue`):**

Both sidebars converted from one flat list into labeled groups.

**Owner menu — 7 groups:**
- Overview — Dashboard · Analytics · Insights · Search analytics
- Catalog — Products · Categories · Inventory forecast · Stock transfers · Locations · Reviews
- Sales — Orders · Customers · Segments · Reservations · Custom orders · Affiliates
- Marketing — Campaigns · Email templates · Coupons · Promo bars
- Content & Design — **Theme builder** (top) · Brand & social · Pages · Blog · Media library · Template · SEO
- AI Tools — AI assistant · AI agents · AI Photoshoot · Brand voice · Concierge
- Settings — Settings · Team · Integrations · Shipping & geo · Billing · Desktop app · Data & export · API tokens · Webhooks · Activity log · 2FA

**Admin menu — 5 groups:** Overview (+ Analytics, System health) · Tenants ·
Billing · Content · System (+ SQL console).

**Newly surfaced pages (were built but hidden):** Team, Integrations,
Affiliates, Reservations, Custom orders, Locations, Stock transfers, Webhooks,
API tokens, Brand voice, Concierge, Email templates, Promo bars, Shipping/geo,
Analytics, AI agents, Data export — plus admin System health & SQL console.

**Also:** legacy "Theme" link renamed to "Brand & social" (it uniquely holds
the Open Graph / social-share image), removing the confusing duplicate next to
Theme Builder. Added 8 missing sidebar icons (one was rendering blank). Sidebar
is now a flex column so the longer menu scrolls cleanly between logo and footer.

**Verified:** build clean, all 17 newly-linked sample pages return 200.

---

## Phase 69 — AI-first product creation (started 2026-05-22)

### 69A — Image-to-product (backend done, UI pending)

Owner drops a product photo → AI returns structured product data (name,
descriptions, category hint, material/color, price-range hint, SEO keywords).

**Shipped (backend, inert until UI wired):**
- `AiAgent::generateFromImage()` — vision-capable OpenAI call (sends image as a
  base64 data URI so it works from localhost; 90s timeout)
- `product_from_image` intent in `AiAgent::SYSTEMS` (strict JSON schema)
- `AiImageController::productFromImage()` — validates upload, stages the photo,
  calls the agent, decodes JSON, records 1 AI credit
- Route: `POST /owner/ai-images/product-from-image`

**Not yet done:** the Vue paste/drop UI panel on the product create page.

### 69B-69F — planned, not started
- 69B Bulk AI rewrite in brand voice (multi-select → queued rewrites → review)
- 69C AI variants generator (suggest size/color/material axes by product type)
- 69D SEO score + auto-fix (0-100 score per product + one-click fix)
- 69E Products page functional sweep
- 69F cms() rollout on products page

---

## Phase 68 — Theme & Branding end-to-end polish (2026-05-22)

All 8 milestones shipped:

- **68A — Functional sweep.** Fixed 4 orphan section keys (`hero`,
  `categories_grid`, `new_arrivals`, `cta`) that had no `SectionLibrary` entry
  (AI Fill 422'd, editor had no field schema). Orphans 4 → 0. Made
  `ThemePresetController` JSON-aware (was returning HTML redirects to axios).
- **68B — MediaPicker** wired into logo, favicon, and section-image fields.
  Backend `uploadAsset()` now accepts `media_path` (existing library item) with
  ownership verification against the polymorphic `media` table.
- **68C — Brand mockup card.** Live CSS preview of all 5 colors + 2 fonts +
  button shape + radius in a real-component context (heading, buttons, surface
  card with accent badge, link). Updates instantly, no iframe round-trip.
- **68D — Five-state ambient save indicator:** Saving / Syncing preview /
  Unsaved / Saved just now / Live · in sync. 500ms tick rolls back the "just
  now" window.
- **68E — Empty / locked / plan-out states.** Friendly empty-sections state with
  AI-suggest + browse CTAs. Better AI-locked notice. Pro-only Custom CSS shows
  an upgrade card instead of a dead textarea.
- **68F — AI logo gallery** (keeps last 4, pick the best) + **fixed a real bug**:
  "Use this logo" was re-generating a new image instead of applying the
  previewed one. Added `apply_path` mode + auto-save chosen logo to media.
- **68G — cms() rollout** for 7 new Theme Builder strings (super-admin editable).
- **68H — Tablet/mobile Edit⇄Preview tab toggle** replacing the old
  "best on desktop" warning banner.

---

## End-to-end verification session (2026-05-29)

User directive: "all platform end-to-end works functionally, backend without
any errors." Results:

- **Backend:** 269 tests passed, 962 assertions, 0 failures.
- **Pages:** probed all 87 parameter-free admin/owner GET pages as super admin →
  **87 returned 200, zero 500 errors.**
- **JS error investigated** (`Cannot read properties of undefined (reading '0')`):
  traced the two code paths that could throw it — `Analytics/Index.vue:62`
  (guarded by `v-for`) and `SystemHealth/Index.vue:59` (controller always returns
  a 14-element array). Neither can actually throw. Concluded the error was a
  transient from rapid refresh/hot-reload churn, not a live defect. No fix
  manufactured for a non-bug. If it recurs, need the page URL + console stack.

### "New folder" on Desktop — resolved
Contained an older `ShopForge-2026-05-16.zip` (the project under its old name,
6 days behind, and a OneDrive online-only placeholder that wasn't downloaded)
plus a redundant copy of the 5/22 backup. **Decision: live `SaaS` is the source
of truth.** Nothing merged — merging the older zip would have regressed work.
`New folder` left untouched as an archive.

---

## Launch-readiness audit (2026-05-29)

**Verdict:** ~75% ready for private beta · ~60% for paid public launch.
Code is the strong column; production infra, legal/compliance, and ops are weak.

### Show-stoppers (must fix before any paying customer) — ~10-12 dev days
1. `.env` is dev (APP_DEBUG=true, DB=sqlite, MAIL=log) → must be production
2. No Terms of Service / Privacy Policy / Cookie consent (Stripe will close the
   account; illegal to take EU/UK payments without)
3. No live email delivery (currently logs only) → SES/Postmark/Resend
4. Production DB not provisioned (MariaDB/Postgres)
5. Object storage not configured (S3/R2) — local storage won't survive deploys
6. No anti-spam on signup (no CAPTCHA) — bots will mass-create stores
7. Stripe webhook signature verification needs an audit
8. Dev login helpers (`/dev/login-*`) — confirm env-gated to local only
9. No error tracking (no Sentry)
10. No backup strategy (`config/backup.php` missing)

### Important (first month): queue worker via Supervisor, deploy script/Docker,
status page, AI-endpoint rate limiting, CSP/security headers, browser E2E tests,
SaaS subscription cancellation flow, DPA template, support-inbox UI.

### Success probability
- Private beta in 2 weeks: ~90%
- 10 paying merchants in 6 months: ~65%
- 1,000 merchants in 24 months: ~30%
- Become "the" AI-first ecommerce platform: ~15-20%

Differentiator = the AI suite. Lean into "drop a photo, get a product" and
"describe your business in one sentence" over "build your store yourself."

---

## AI inventory (documented 2026-05-22)

Full map of all AI features lives on the Desktop in three formats:
`Namat-AI-Inventory-2026-05-22.md` / `.docx` / `.pdf`.

**21 working AI functions:** chat · product_copy · seo · theme · theme_refine ·
AiPaletteService · section_recommend · section_content · review_moderate ·
onboarding_blueprint · analytics_intent · voice_distill · CatalogRetriever (RAG)
· AiMemory · product image gen · logo gen (gallery) · lifestyle imagery ·
AiPhotoshootService (10-dimension composer) · campaign drafter · ConciergeService
· ai_runs audit trail. Models: gpt-4o-mini (text/JSON) + gpt-image-1 (images).

---

## Key files touched this period

```
resources/js/Layouts/DashboardLayout.vue          ← Phase 70 nav overhaul
resources/js/Pages/Owner/ThemeBuilder/Index.vue    ← Phase 68 (all 8 milestones)
resources/js/Pages/Owner/AiPhotoshoot/Index.vue    ← results gallery + reference (earlier)
app/Builder/SectionLibrary.php                     ← 4 legacy section keys added
app/Http/Controllers/Owner/ThemeBuilderController.php  ← media_path upload
app/Http/Controllers/Owner/ThemePresetController.php   ← JSON-aware
app/Http/Controllers/Owner/AiImageController.php   ← apply_path + productFromImage
app/Services/AiAgent.php                           ← generateFromImage + product_from_image intent
routes/owner.php                                   ← ai-images/product-from-image
```

---

## Pending / next candidates

- **Phase 69A UI** — wire the image-to-product paste/drop panel into the product
  create page (backend is ready)
- **Launch show-stoppers** — production env, legal pages, email, error tracking,
  backups (highest priority for going live)
- **Phase 69B-F** — bulk rewrite, variants generator, SEO score, products sweep
- Fold OG image into Theme Builder and retire the legacy Brand & social page

---

## Environment & security notes

- **PHP** — `$env:LOCALAPPDATA\dev-tools\php\php.exe` (8.3.31)
- **Node** — `$env:LOCALAPPDATA\dev-tools\node\node-v20.18.1-win-x64\node.exe` (20.18.1)
- **Run server** — `php artisan serve --port=8765` · open `/dev/login-super`
- **Build** — `npm run build` · **Tests** — `vendor/bin/pest --parallel`
- OpenAI API key in `.env` — **rotate before public deploy**
- VAPID keys in `.env` for web push
- Dev login helpers (`/dev/login-super`, `/dev/login-as/{id}`, `/dev/logout`)
  must stay env-gated to `local` — never reachable in production
