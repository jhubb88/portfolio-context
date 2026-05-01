# Project Context — jimmyhubbard2.cc Portfolio
Last updated: 2026-05-01

---

## What's Working

| App | CloudFront URL | Status |
|-----|----------------|--------|
| WordPress site | https://jimmyhubbard2.cc | Live, HTTPS ✅ |
| NTCIP Traffic Controller Simulator | https://d1r8pxnmau5sot.cloudfront.net | Live, HTTPS ✅ |
| Live Traffic Dashboard | https://d1t5py05a4uugi.cloudfront.net | Live, HTTPS ✅ |
| Text to Audio | https://d2ey5cipu3t9y.cloudfront.net | Live, HTTPS ✅ |
| AI Log Analyzer | https://dn6duxmzpvyau.cloudfront.net | Live, HTTPS ✅ |
| AI Resume Matcher | https://d3t6z67os7y9is.cloudfront.net | Live, HTTPS ✅ |
| Advanced Projects (linked from "Cloud & AI Systems (AWS)" card on Projects page) | https://d2uisqfxjzeo6a.cloudfront.net | Live, HTTPS ✅ |
| RAG Knowledge Chatbot | https://d1r1qv7io7k8vk.cloudfront.net | Live, HTTPS ✅ (v1.2-bedrock-only shipped 2026-04-27 — SambaNova fully removed from frontend + Lambda; iPad Safari freeze after Clear Session is a known deferred issue) |

### CloudFront Stack
- Stack name: `jimmy-cloudfront-distributions`
- All 6 active projects have CloudFront distributions provisioned
- WordPress Projects page updated with CloudFront URLs (no S3 URLs remain)

### CloudFront Distribution IDs & S3 Buckets

| App | Distribution ID | S3 Bucket | CloudFront Domain |
|-----|----------------|-----------|-------------------|
| NTCIP Simulator | `E2PEIMT1J3W4MO` | `jimmy-ntcip-simulator` | d1r8pxnmau5sot.cloudfront.net |
| Live Traffic Dashboard | `E3H1V6C42HG9P1` | `jimmy-traffic-dashboard` | d1t5py05a4uugi.cloudfront.net |
| Text to Audio | `E1BM7FLW1T9GAM` | `jimmy-text-to-audio` | d2ey5cipu3t9y.cloudfront.net |
| AI Log Analyzer | `E3Q8ZCVRAS854T` | `jimmy-log-analyzer` | dn6duxmzpvyau.cloudfront.net |
| AI Resume Matcher | `E3H0XAJDR3BQG1` | `jimmy-resume-matcher` | d3t6z67os7y9is.cloudfront.net |
| Advanced Projects | `E1VZ0ELKDC3LN0` | `jimmy-advanced-projects` | d2uisqfxjzeo6a.cloudfront.net |
| FieldIQ | `E12Z80TRB0P2XR` | `jimmy-fieldiq` | d1q3x6tsvbllgg.cloudfront.net |
| RAG Chatbot | `EN88LEBW14923` | `rag-chatbot-603509861186-dev` | d1r1qv7io7k8vk.cloudfront.net |

### Favicons (2026-04-21)
- All 6 portfolio apps have a full favicon set: `favicon.ico`, `favicon-16.png`, `favicon-32.png`, `apple-touch-icon.png`, `icon-192.png`, `icon-512.png`
- All favicon files live at the repo/S3 root (not in a subdirectory)
- Standard `<link>` block inserted in each `index.html` after viewport `<meta>`, before `<title>`
- NTCIP Simulator: also updated `manifest.json` and `sw.js` (PWA) to point at root-level icons; SW cache bumped `ntcip-sim-v1` → `ntcip-sim-v2`; dead `icons/` directory removed

### Deploy Pipelines

Originally a single workflow in `jhubb88/ClaudeCode/.github/workflows/deploy.yml` deployed advanced-projects + 5 standalone projects (text-to-audio, log-analyzer, resume-matcher, traffic-dashboard, ntcip-simulator) by checking out *stale stub copies* under `Desktop/ClaudeCode/<project>/`. Bug found 2026-04-30 (advanced-projects); audit 2026-05-01 confirmed the same bug class on the other 5. Fix pattern: each project gets its own `deploy.yml` in its standalone repo, the ClaudeCode step + matching CloudFront invalidation are removed, and the orphan stub directory is deleted.

- **Advanced Projects (fixed 2026-04-30):** `jhubb88/advanced-projects/.github/workflows/deploy.yml` — push to main → `aws s3 cp index.html s3://jimmy-advanced-projects/index.html` → invalidate `E1VZ0ELKDC3LN0`.
- **Text to Audio (fixed 2026-05-01):** `jhubb88/text-to-audio/.github/workflows/deploy.yml` — push to main → `aws s3 sync . s3://jimmy-text-to-audio/ --delete` (excludes `.git/*`, `.github/*`, `*.md`, `LICENSE`, `.gitignore`) → invalidate `E1BM7FLW1T9GAM`. README deploy section also updated to reflect CI/CD.
- **Pending same fix:** log-analyzer, resume-matcher, traffic-dashboard, ntcip-simulator (still deploy from stale stubs in ClaudeCode).
- **FieldIQ:** `jhubb88/FieldIQ/.github/workflows/prewarm.yml` — weekly Sunday 02:00 UTC + `workflow_dispatch`. NOT triggered on push, so commits between runs are silently undeployed until the next Sunday or manual dispatch.
- **RAG Knowledge Chatbot:** no deploy workflow exists; S3 last touched 2026-04-19 (predates frontend "Generating answer" polish in `1cc5ccc`).
- **Linux Ops Command Copilot:** no deploy workflow; last manual deploy 2026-04-27 (currently in sync, but no automation).

### WordPress Projects Page (Page ID 43) — Layout
Grid order as of 2026-04-19:
1. Cloud & AI Systems (AWS) → links to https://d2uisqfxjzeo6a.cloudfront.net (Advanced Projects hosted app)
2. NTCIP Traffic Controller Simulator (TCS)
3. Live Traffic Dashboard
4. Log Analyzer
5. AI Resume ↔ Job Matcher
6. Text to Audio

- 2026-04-19: Renamed position-1 card "Advanced Projects" → "Cloud & AI Systems (AWS)", moved from pos 4 → pos 1, icon 🚀 → ☁️, blurb rewritten to surface AWS/Bedrock/Lambda/API Gateway/SambaNova keywords. Detail panel `<h4>` and `<p>` updated to match. `data-project="advanced-projects"` attribute, `#desc-advanced-projects` ID, and destination CloudFront URL were intentionally NOT changed — click-wiring JS depends on them.
- 2026-04-19 (later same day, 5 follow-up changes after commit b7accbf):
  - Wired LIVE APP pills on 5 cards (ntcip-simulator, traffic-dashboard, log-analyzer, resume-matcher, text-to-audio) to open their app URLs directly in a new tab via `onclick="event.stopPropagation(); window.open(url, '_blank', 'noopener');"`. Pills kept as `<div class="tag">` (not `<a>`) per wpautop constraint.
  - Added DETAILS → button on same 5 cards inside a new `<div class="card-actions">` wrapper. DETAILS button has no onclick — relies on event bubbling to existing card-level click handler, which opens the detail panel. New CSS added for `.project-card .card-actions` (flex row, gap 8px, justify-content center, flex-wrap) and `.project-card .tag.tag-details` (transparent background, 2px rgba(255,255,255,0.3) border, light text, padding 2px 12px) plus hover state.
  - Bottom-aligned action rows across all 6 cards: changed `.project-card .card-actions` margin-top from 14px to `auto`. This pins buttons to the bottom of each card regardless of blurb length (Text to Audio previously floated higher due to shorter blurb).
  - Wrapped Cloud & AI Systems card's bare LIVE APP pill in `<div class="card-actions">` for structural consistency with other 5 cards.
  - Removed duplicate unscoped `.card-actions` and `.card-actions .tag` CSS rules (hygiene — they were dead duplicates of the `.project-card`-prefixed versions).
  - Simplified Cloud & AI Systems card (Fix 2): added `onclick` to its LIVE APP pill (same pattern, URL = https://d2uisqfxjzeo6a.cloudfront.net). Added branch to card-level click handler so clicking the Cloud & AI card background navigates directly to Advanced Projects page instead of opening a (now-removed) detail panel. Deleted the entire `<div class="project-desc" id="desc-advanced-projects">` block. Rationale: that panel had no unique content — only an "Explore Projects →" button pointing to the same URL. Dead layer removed.

  Net result: all 6 cards now have two click paths that each lead somewhere useful. LIVE APP → direct to app (new tab). Card background → detail panel on 5 cards, direct to Advanced Projects on Cloud & AI. DETAILS → button exists only on the 5 cards that have detail panels worth opening.

- 2026-04-20 — Projects page visual pass (2 of 3 changes shipped):
  - **Change #1 — Detail Panel Restyle (SHIPPED)**
    - Target: `.project-desc` (5 panels: desc-ntcip-simulator, desc-traffic-dashboard, desc-log-analyzer, desc-resume-matcher, desc-text-to-audio)
    - CSS before: `background: #f8f9fc; border-left: 4px solid #e94560; border-radius: 0 12px 12px 0; padding: 24px 28px`
    - CSS after: `background: #fafaf7; border: 1px solid #e5e0d6; border-radius: 16px; padding: 32px; box-shadow: 0 4px 20px rgba(26,26,46,0.08)`
    - Added rule: `.project-desc h4 .panel-label { color: #6b6b6b; font-weight: 500; margin-right: 4px; }` — styles the "About:" label prefix in each panel h4
    - HTML: wrapped "About:" in each of the 5 `<h4>` elements with `<span class="panel-label">`
    - Also edited 5 `.waf-section` inline styles: `border-top: 2px solid #e2e8f0` → `border-top: 1px solid #e5e0d6` to match new warm palette
    - No `!important` used — inline styles edited at source
  - **Change #2 — Page Header Treatment (DEFERRED)**
    - Initial intent: restyle the "Projects" h1 to feel intentional (plain black, weight 600, 36px — reads as WP default)
    - Discovery: header is a site-wide WP block post-title (`wp-block-post-title has-large-font-size`) rendered the same way on Home, Resume, About, Contact. Changing it on Page 43 only would either be overridden by theme specificity or create cross-page inconsistency.
    - CC proposed three treatment options (weight+accent with pink underline, oversized editorial, dual-tone split). No option selected — owner deferred the entire header treatment pending a site-wide evaluation across Home/Projects/Resume/About/Contact.
    - Decision: defer to a dedicated site-wide theme-level effort evaluating all 5 page titles simultaneously
  - **Change #3 — Cloud & AI Card EXPLORE Badge (SHIPPED)**
    - Target: `[data-project="advanced-projects"]` card only — no other card touched
    - Added `[data-project="advanced-projects"] { position: relative; }` — scoped, not applied to global `.project-card`
    - Added `.explore-badge { position: absolute; top: 12px; right: 12px; background: transparent; border: 1px solid #a8dadc; color: #a8dadc; font-size: 0.7rem; font-weight: 600; padding: 4px 10px; border-radius: 20px; letter-spacing: 0.05em; text-transform: uppercase; cursor: pointer; }`
    - Added HTML: `<span class="explore-badge">EXPLORE →</span>` as first child of Cloud & AI card div
    - Color `#a8dadc` reused from `.waf-btn` (architecture pills inside detail panels) — no new palette color introduced
    - Click: badge inherits the card's existing click-to-navigate handler; `cursor: pointer` reflects actual behavior
  - **Design decisions recorded:**
    - Kept two distinct aesthetics (Projects = card grid portfolio convention, Advanced = node graph deep-dive showcase). EXPLORE badge telegraphs the shift before clickthrough rather than forcing Projects page into terminal aesthetic.
    - Chose Option B (elevated off-white panel `#fafaf7`) over Option A (dark navy matching cards) for detail panels — creates visual hierarchy: cards = project surface, panels = info layer.
    - Badge only on Cloud & AI card (no outline/border combo) — badge alone carries both visual emphasis and behavioral differentiation. Outline would be redundant.
    - Pink (`#e94560`) reserved as single-purpose CTA color (LIVE APP, DETAILS, Visit App). Cyan `#a8dadc` used for badge to avoid diluting pink's role.
    - Retained click-anywhere-on-card behavior — badge is a signal, not a gate. Restricting clicks would break accessibility and punish imprecise clicks.
  - **Open backlog items:**
    - Site-wide page header treatment (Home/Projects/Resume/About/Contact — must be evaluated together)
    - Detail panel: 4 architecture badges produce a 3+1 orphan row at bottom — count issue, not a styling bug. Defer unless owner flags it.

### AWS Infrastructure (Log Analyzer)
- Lambda: `jimmy-log-analyzer` (python3.12, 30s timeout, Anthropic API)
- API Gateway: `jimmy-log-analyzer-api` (ID: 90rplm0obh)
- API URL: `https://90rplm0obh.execute-api.us-east-1.amazonaws.com/prod/analyze`
- IAM Role: `jimmy-log-analyzer-role`

### AWS Infrastructure (RAG Chatbot)
- Region: us-east-1
- Stack: `rag-knowledge-chatbot` — live in us-east-1 (live stack; do NOT confuse with abandoned `rag-chatbot-dev` REVIEW_IN_PROGRESS shell)
- S3 Bucket: `rag-chatbot-603509861186-dev`
- API Endpoint: `https://uiauqskgv0.execute-api.us-east-1.amazonaws.com/dev/query` (Bedrock Claude Haiku 4.5 only as of v1.2)
- Frontend URL (CloudFront): `https://d1r1qv7io7k8vk.cloudfront.net` (distribution EN88LEBW14923)
- Frontend URL (S3 raw): `http://rag-chatbot-603509861186-dev.s3-website-us-east-1.amazonaws.com/frontend/index.html`
- Ingest Lambda: `rag-chatbot-ingest-dev` (deployed, tested, confirmed working)
- Query Lambda: `rag-chatbot-query-dev` (Bedrock-only as of v1.2; 1024MB, module-level index caching)
- Titan Embeddings v2: ✅ LIVE
- Bedrock generation: ✅ `us.anthropic.claude-haiku-4-5-20251001-v1:0` — sole provider
- EventBridge warm-up: `rag-chatbot-warmup-dev` — pings Lambda every 5 min; warmup branch primes S3 index cache only (no synthetic provider ping); REPORT 1–4ms warm, ~4s cold
- SSM (DORMANT post-v1.2 — kept for rollback only): SambaNova key at /rag-chatbot/sambanova-api-key; Nebius key at /rag-chatbot/nebius-api-key (do not delete before 2026-05-19)
- GitHub repo: jhubb88/aws-rag-chatbot
- Tags: v0.3-ingest, v0.4-query, v0.5-frontend, v0.6-retrieval-tuning, v0.7-observability, v0.8-integration, v0.9-polish, v1.0-multikb, v1.1-sambanova, v1.2-bedrock-only
- Vector index: 2,027 chunks (jimmy_background: 43 + curated; aws_well_architected: 1,974) at ~58MB
- Knowledge bases: Jimmy's background + AWS Well-Architected Framework (6 pillar whitepapers)
- CloudWatch dashboard: `RAG-Chatbot-Dashboard` (4 widgets)
- CloudWatch alarms: `rag-chatbot-error-rate-dev` (>5%), `rag-chatbot-p95-duration-dev` (>12s) — both OK
- SNS topic: `RAG-Chatbot-Alerts-dev` → jimmy.hubbard0813@gmail.com ✅ confirmed
- IaC drift: live stack `rag-knowledge-chatbot` has drifted from local template (parameters, env vars, missing warmup resources, stale Lambda code S3 reference). Phase 12 candidate — full inventory in `BUILD_LOG.md`. Until realigned, all infra changes via direct CLI, not `cloudformation update-stack`.

---

## In Progress / Next Steps

### RAG Knowledge Chatbot
- **Status: v1.2-bedrock-only shipped 2026-04-27** — SambaNova fully removed from frontend, Lambda, and live API response payload
- Bedrock instruction-tuned: concision, 2–3 paragraphs, `max_tokens=384`
- Latency (steady-state): warm 5–6s; first-touch cold ~4.7s Lambda (index pre-warmed by warmup ping)
- 2026-04-27: Bedrock-only rework — frontend strip commit `430d8ab`, Lambda strip commit `636891c`, docs sweep commit `694dc09` tagged `v1.2-bedrock-only`. Frontend 1443→1347 lines, Lambda 468→300 lines, `engine_used` removed from API response. Rationale: SambaNova free-tier 20 RPD insufficient and Developer Tier auto-upgrade broken in their billing migration — community forum confirms widespread issue.
- 2026-04-19: iOS CSS fixes — iPhone input auto-zoom fix (16px font-size on mobile inputs, commit `77c7c82`), global `100dvh` fix for iOS viewport height mismatch. iPad Safari freeze after Clear Session investigated, not resolved, deferred as low-priority.
- 2026-04-18: top_k raised 3→5 (commit `b30b6ab`) — fixes projects-list retrieval miss.
- 2026-04-24: Advanced-projects page re-scoped to Bedrock-only framing — summary, tags, keyPoints, and all 6 architecture modal sections rewritten.
- **Phase 12 deferred:** IaC drift realignment. Live `rag-knowledge-chatbot` CFN stack has drifted from local template across three axes (parameter names, Lambda env vars, missing warmup resources). Lambda code reference also stale (S3 zip is pre-v1.1; live Lambda runs Bedrock-only code via direct `update-function-code`). Functional behavior unaffected. Estimated 4–6 hour dedicated session. Full inventory in BUILD_LOG.md Phase 12 candidate.
- Next active work: index format optimization (JSON→NumPy), ingest path normalization, response streaming

### Advanced Projects Page Header Rename
- **Status: PENDING (scheduled for 2026-04-20)**
- Rename page header on Advanced Projects hosted app (https://d2uisqfxjzeo6a.cloudfront.net) from "Advanced Projects" → "Cloud & AI Systems" to match the renamed entry card on the Projects grid
- Handled in the separate "Advanced Projects" CC chat, not the Projects-grid chat
- Rationale: card on Projects grid now reads "Cloud & AI Systems (AWS)" — destination page header should match to avoid recruiter-facing naming mismatch

### Projects Page Full Visual Redesign
- **Status: NOT STARTED**
- Goal: Unify visual language between Projects page and Advanced Projects page. Currently the Projects page uses a light/beige background while the Advanced page is pure black with terminal-style grid. Pages feel like they belong to different sites.
- Scope: background + palette + typography unification on Projects page to match Advanced page DNA. Keep card layout, emoji icons, button structure — only change the wrapper/theme.
- Approach: one visual change per CC prompt (background first, then palette, then typography). Do NOT bundle everything into one PATCH.
- Trigger: handled in a dedicated chat after 2026-04-20 Advanced page header rename is complete.

### Custom Domain via CloudFront
- **Status: NOT STARTED**
- Goal: Wire jimmyhubbard2.cc subpaths or subdomains through CloudFront
- DNS is on Namecheap
- CloudFront distributions already live for all 6 projects
- Requires: ACM cert, CloudFront alternate domain name config, Namecheap CNAME records

---

## Repository Map

| Project | GitHub Repo | Local Path |
|---------|-------------|------------|
| Advanced Projects page | jhubb88/advanced-projects | Desktop/Projects/advanced-projects/ |
| NTCIP Simulator | jhubb88/ntcip-simulator | Desktop/Projects/ntcip-simulator/ |
| Live Traffic Dashboard | jhubb88/traffic-dashboard | Desktop/Projects/traffic-dashboard/ |
| Text to Audio | jhubb88/text-to-audio | Desktop/Projects/text-to-audio/ |
| AI Log Analyzer | jhubb88/log-analyzer | Desktop/Projects/log-analyzer/ |
| AI Resume Matcher | jhubb88/resume-matcher | Desktop/Projects/resume-matcher/ |
| FieldIQ | jhubb88/FieldIQ | Desktop/FieldIQ/ (note: NOT inside Projects/) |
| RAG Knowledge Chatbot | jhubb88/aws-rag-chatbot | Desktop/Projects/rag-chatbot/ |
| Portfolio Context | jhubb88/portfolio-context | Desktop/Projects/portfolio-context/ |
| Website (WP snapshot) | jhubb88/Website | N/A |

---

## Key Config / Gotchas

| Item | Value |
|------|-------|
| WordPress admin | https://jimmyhubbard2.cc/wp-admin (user: adminJimmy) |
| WP REST API auth | Application Password named "Claude" (or session nonce via Playwright) |
| Home page ID | 29 |
| Projects page ID | 43 |
| GitHub org | github.com/jhubb88 |
| AWS region | us-east-1 |
| AWS account ID | 603509861186 |
| AWS CLI profile | portfolio-user |

**WP quirk:** Use `div + onclick` for clickable project cards — WordPress wpautop filter breaks anchor tags inside block-level elements.

**WP REST API via Playwright:** When logged in via Playwright, use `page.evaluate` with `fetch('/wp-json/wp/v2/pages/ID', { headers: { 'X-WP-Nonce': nonce } })` — auth cookies are HttpOnly so they can't be passed via curl. Get nonce with `wpApiSettings.nonce`.

**Skills card color reference:**
- Gold border: `#c9a227`
- Icon badge / highlight pill: `#1a1a2e`
- Regular tag background: `#f0f2f8`
- Font Awesome CDN: `https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css`

**GitHub auth (recurring):** gh CLI
