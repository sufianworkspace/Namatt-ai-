# Namat AI — Progress Snapshot · 2026-05-30

Multi-tenant SaaS ecommerce platform (Laravel 11 + Vue 3 + Inertia + Tailwind +
SQLite). This file captures the state at the end of the 2026-05-30 session,
including the in-progress GitHub push (paused on auth) and all preceding work.

---

## At-a-glance

| | |
|---|---|
| **Tests** | 269 passing · 962 assertions · `pest --parallel` · 0 failures |
| **Build** | `npm run build` clean (~18s) |
| **Pages verified** | All 87 parameter-free admin/owner GET pages return 200 · 0 server errors |
| **Languages** | English + Arabic (RTL) at parity |
| **Demo Pro store** | `demo-pro@namat.test` / `password` · subdomain `aurorapro` · store 9 · user 12 |
| **Super admin** | `done.3kk@gmail.com` / `NamatAdmin@2026` |
| **Dev server** | `php artisan serve --port=8765` · login via `/dev/login-super` |
| **Backend size** | 89 models · 124 controllers · 59 services · 92 migrations · 462 routes |
| **Frontend size** | 119 Vue pages · 11 components · 152 Blade views · 14 storefront flavors |

---

## 🟡 In-flight — GitHub push (paused on user auth)

**Local repo is fully prepared. Push is blocked only on the user completing
GitHub authentication (interactive, can't be automated).**

**Done in this session:**
- Installed Git for Windows 2.54.0 via winget → `C:\Program Files\Git\cmd\git.exe`
- Installed GitHub CLI 2.93.0 via winget → `C:\Program Files\GitHub CLI\gh.exe`
- Hardened `.gitignore` — added `/build` (was about to commit 2018 files of
  PHPStan cache totalling 57.8 MB) and `/database/*.sqlite` variants (was about
  to commit the dev database with the super-admin hash + demo data)
- `git init` + `git branch -M main`
- Safety-verified: `.env`, `database/database.sqlite`, `vendor/`, `node_modules/`
  all confirmed ignored via `git check-ignore`
- First commit: **`69edd75`** — "Initial commit - Namat AI multi-tenant SaaS
  ecommerce platform" — 873 files (down from 2891 after build cache excluded)
- Identity set: `user.name = q.1337.h`, `user.email = q.1337.h@gmail.com`

**Pending (user action):**
1. Open a fresh PowerShell window (Start → "PowerShell" → Enter). New window
   needed so git+gh appear on PATH.
2. `gh auth login` → `GitHub.com` → `HTTPS` → `Yes` → `Login with a web browser`
   → paste the one-time code at `github.com/login/device` → Authorize.
   ⚠️ Must be **github.com**, not Microsoft Copilot or any Microsoft login.
3. Confirm with `gh auth status` — should show "Logged in to github.com as …"

**Then push** (one command, can be run by either side):
```powershell
gh repo create namat-ai --private --source=. --remote=origin --push
```
Creates a private repo named `namat-ai` and pushes `main` in one shot.

**⚠️ Security reminder:** even though `.env` is gitignored, the OpenAI API key
that's been in it should be **rotated** before any production deploy. Keep the
repo **private**.

---

## Phase 70 — Dashboard navigation overhaul (done · 2026-05-29)

**The find:** 23 fully-built, working pages were not linked in the sidebar.
They returned 200 but were unreachable from the menu. That's why "Theme Builder
seemed missing" — it was buried in a flat 26-item list next to a confusingly-
named legacy "Theme" link.

**Changes to `resources/js/Layouts/DashboardLayout.vue`:**

Owner sidebar regrouped into 7 sections (Overview · Catalog · Sales · Marketing
· Content & Design · AI Tools · Settings). Admin sidebar into 5 (Overview ·
Tenants · Billing · Content · System).

**Newly surfaced (were built but hidden):** Team, Integrations, Affiliates,
Reservations, Custom orders, Locations, Stock transfers, Webhooks, API tokens,
Brand voice, Concierge, Email templates, Promo bars, Shipping/geo, Analytics,
AI agents, Data export · plus admin System health & SQL console.

**Also:** legacy "Theme" renamed to "Brand & social" (it uniquely holds the OG/
social-share image). Theme Builder now top of Content & Design. Added 8 missing
sidebar icons (one was blank). Sidebar is now `flex flex-col` so longer menus
scroll cleanly between logo and footer.

**Verified:** build clean, all 17 newly-linked sample pages return 200.

---

## Phase 69 — AI-first product creation (started 2026-05-22)

### 69A — Image-to-product (backend done, UI pending)

Owner drops a product photo → AI returns structured product data.

**Shipped (backend, inert until UI wired):**
- `AiAgent::generateFromImage()` — vision-capable OpenAI call (base64 data URI,
  works from localhost; 90s timeout)
- `product_from_image` intent in `AiAgent::SYSTEMS` (strict JSON schema)
- `AiImageController::productFromImage()` — validates upload, stages photo,
  decodes JSON, records 1 AI credit
- Route: `POST /owner/ai-images/product-from-image`

**Not yet done:** the Vue paste/drop UI on the product create page.

### 69B–F — planned, not started
- 69B Bulk AI rewrite in brand voice
- 69C AI variants generator
- 69D SEO score + auto-fix
- 69E Products page functional sweep
- 69F cms() rollout on products page

---

## Phase 68 — Theme & Branding end-to-end polish (done · 2026-05-22)

All 8 milestones shipped:

- **68A** — fixed 4 orphan section keys (`hero`, `categories_grid`,
  `new_arrivals`, `cta`) that had no `SectionLibrary` entry; `ThemePresetController`
  now JSON-aware
- **68B** — MediaPicker wired into logo, favicon, and section-image fields;
  `uploadAsset()` accepts `media_path` with ownership verification
- **68C** — live brand-mockup card (5 colors + 2 fonts + button shape +
  radius in a real-component context, CSS-bound, no iframe)
- **68D** — 5-state ambient save indicator
- **68E** — empty / locked / plan-out states polished
- **68F** — AI logo gallery (last 4) + fixed a real bug (Use this logo was
  regenerating instead of applying); auto-save chosen logo to media library
- **68G** — 7 new `cms()` keys (super-admin editable)
- **68H** — tablet/mobile Edit⇄Preview tab toggle

---

## End-to-end verification (2026-05-29)

- 269 tests passed, 962 assertions, 0 failures
- All 87 parameter-free admin/owner GET pages returned 200, 0 server errors
- JS error "Cannot read properties of undefined (reading '0')" investigated and
  traced to no live code path — transient from refresh/HMR churn, no fix
  needed (the two `[0]` candidates are guarded by `v-for` and controller
  guarantees respectively)
- "New folder" on Desktop resolved: contained an older `ShopForge-2026-05-16.zip`
  (online-only OneDrive placeholder) and a redundant 5/22 backup. Live `SaaS`
  is the source of truth; nothing merged in. `New folder` left untouched.

---

## Launch-readiness audit (2026-05-29)

**Verdict:** ~75% private beta · ~60% paid public launch.

### Show-stoppers (~10-12 dev days)
1. `.env` is dev (APP_DEBUG, SQLite, MAIL=log) → must go prod
2. No Terms / Privacy / Cookie consent (Stripe will close the account)
3. No live email delivery → SES/Postmark/Resend
4. Prod DB not provisioned (MariaDB/Postgres)
5. Object storage not configured (S3/R2)
6. No CAPTCHA on signup (bots will exploit)
7. Stripe webhook signature verification audit
8. Confirm dev login helpers env-gated
9. No error tracking (Sentry)
10. No backup strategy

### Probability
- Private beta in 2 weeks: ~90%
- 10 paying merchants in 6 months: ~65%
- 1,000 merchants in 24 months: ~30%

---

## AI inventory — 21 working functions

Full map on Desktop in three formats:
`Namat-AI-Inventory-2026-05-22.md` / `.docx` / `.pdf`.

chat · product_copy · seo · theme · theme_refine · AiPaletteService ·
section_recommend · section_content · review_moderate · onboarding_blueprint ·
analytics_intent · voice_distill · CatalogRetriever (RAG) · AiMemory · product
image gen · logo gen (gallery) · lifestyle imagery · AiPhotoshootService ·
campaign drafter · ConciergeService · ai_runs audit trail.

Models: gpt-4o-mini (text/JSON) + gpt-image-1 (images).

---

## Key file paths

```
resources/js/Layouts/DashboardLayout.vue                ← Phase 70 nav overhaul
resources/js/Pages/Owner/ThemeBuilder/Index.vue          ← Phase 68
resources/js/Pages/Owner/AiPhotoshoot/Index.vue          ← results gallery
app/Builder/SectionLibrary.php                            ← legacy keys added
app/Http/Controllers/Owner/ThemeBuilderController.php    ← media_path upload
app/Http/Controllers/Owner/ThemePresetController.php     ← JSON-aware
app/Http/Controllers/Owner/AiImageController.php         ← apply_path + productFromImage
app/Services/AiAgent.php                                  ← generateFromImage + product_from_image intent
routes/owner.php                                          ← ai-images/product-from-image
.gitignore                                                ← + /build, /database/*.sqlite*
```

---

## Pending / next candidates

- **Resume GitHub push** — once user completes `gh auth login`, run
  `gh repo create namat-ai --private --source=. --remote=origin --push`
- **Phase 69A UI** — wire the image-to-product panel into product create
  (backend is ready)
- **Launch show-stoppers** — production env, legal pages, email, error
  tracking, backups (highest priority for going live)
- **Phase 69B-F** — bulk rewrite, variants generator, SEO score, products sweep
- Fold OG image into Theme Builder and retire legacy Brand & social page

---

## Environment & security

- **PHP** — `$env:LOCALAPPDATA\dev-tools\php\php.exe` (8.3.31)
- **Node** — `$env:LOCALAPPDATA\dev-tools\node\node-v20.18.1-win-x64\node.exe` (20.18.1)
- **Git** — `C:\Program Files\Git\cmd\git.exe` (2.54.0) ← installed today
- **gh** — `C:\Program Files\GitHub CLI\gh.exe` (2.93.0) ← installed today
- **Run server** — `php artisan serve --port=8765` · open `/dev/login-super`
- **Build** — `npm run build` · **Tests** — `vendor/bin/pest --parallel`
- OpenAI API key in `.env` — **rotate before public deploy**
- VAPID keys in `.env` for web push
- Dev login helpers (`/dev/login-super`, `/dev/login-as/{id}`, `/dev/logout`)
  must stay env-gated to `local` only
