# Project Context — jimmyhubbard2.cc Portfolio
Last updated: 2026-04-19

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
| Advanced Projects | https://d2uisqfxjzeo6a.cloudfront.net | Live, HTTPS ✅ |
| RAG Knowledge Chatbot | https://d1r1qv7io7k8vk.cloudfront.net | Live, HTTPS ✅ (SambaNova swap complete — multi-KB, dual-provider, mobile responsive) |

### CloudFront Stack
- Stack name: `jimmy-cloudfront-distributions`
- All 6 active projects have CloudFront distributions provisioned
- WordPress Projects page updated with CloudFront URLs (no S3 URLs remain)

### AWS Infrastructure (Log Analyzer)
- Lambda: `jimmy-log-analyzer` (python3.12, 30s timeout, Anthropic API)
- API Gateway: `jimmy-log-analyzer-api` (ID: 90rplm0obh)
- API URL: `https://90rplm0obh.execute-api.us-east-1.amazonaws.com/prod/analyze`
- IAM Role: `jimmy-log-analyzer-role`

### AWS Infrastructure (RAG Chatbot)
- Region: us-east-1
- Stack: `rag-knowledge-chatbot` — live in us-east-1
- S3 Bucket: `rag-chatbot-603509861186-dev`
- API Endpoint: `https://uiauqskgv0.execute-api.us-east-1.amazonaws.com/dev/query` (live — SambaNova Llama 3.3 + Bedrock Claude Haiku 4.5)
- Frontend URL (CloudFront): `https://d1r1qv7io7k8vk.cloudfront.net` (distribution EN88LEBW14923)
- Frontend URL (S3 raw): `http://rag-chatbot-603509861186-dev.s3-website-us-east-1.amazonaws.com/frontend/index.html`
- Ingest Lambda: `rag-chatbot-ingest-dev` (deployed, tested, confirmed working)
- Query Lambda: `rag-chatbot-query-dev` (deployed, live, 1024MB, multi-provider router, module-level index caching)
- Titan Embeddings v2: ✅ LIVE
- Bedrock generation: ✅ `us.anthropic.claude-haiku-4-5-20251001-v1:0` — default provider on page load
- SambaNova Llama 3.3-70B: ✅ LIVE generation path (Provider B, swapped from Nebius 2026-04-19)
- EventBridge warm-up: `rag-chatbot-warmup-dev` — pings Lambda every 5 min; warmup branch sends synthetic SambaNova ping (~631ms) to keep inference endpoint warm
- SSM: SambaNova API key at /rag-chatbot/sambanova-api-key (active); Nebius key at /rag-chatbot/nebius-api-key (dormant rollback — do not delete before 2026-05-19)
- GitHub repo: jhubb88/aws-rag-chatbot
- Tags: v0.3-ingest, v0.4-query, v0.5-frontend, v0.6-retrieval-tuning, v0.7-observability, v0.8-integration, v0.9-polish, v1.0-multikb
- Vector index: 2,027 chunks (jimmy_background: 43 + curated; aws_well_architected: 1,974) at ~58MB
- Knowledge bases: Jimmy's background + AWS Well-Architected Framework (6 pillar whitepapers)
- CloudWatch dashboard: `RAG-Chatbot-Dashboard` (4 widgets)
- CloudWatch alarms: `rag-chatbot-error-rate-dev` (>5%), `rag-chatbot-p95-duration-dev` (>12s) — both OK
- SNS topic: `RAG-Chatbot-Alerts-dev` → jimmy.hubbard0813@gmail.com ✅ confirmed

---

## In Progress / Next Steps

### RAG Knowledge Chatbot
- **Status: SambaNova provider swap complete (2026-04-19)**
- Both providers instruction-tuned: Bedrock (concision, 2–3 paragraphs, `max_tokens=384`), SambaNova/Llama (`max_tokens=256`, natural synthesis)
- Warm baseline (measured 2026-04-19): Bedrock 2.8–4.9s, SambaNova 2.5–3.1s. First-call after idle: Bedrock 4.86s, SambaNova 5.66s. Latency is comparable; Bedrock remains default because it produces ~2-3x longer biographical answers with more specific citations.
- 2026-04-19: Provider B swapped Nebius → SambaNova (commits `8879df9`, `4be78af`, `3cc3f87`). Root cause: Nebius warmup was timing out at 20s, breaching the 12s p95 alarm. SambaNova warmup completes in ~631ms. p95 alarm resolved.
- 2026-04-18: top_k raised 3→5 (commit `b30b6ab`) — fixes projects-list retrieval miss. Both providers now name all 7 projects.
- Curated content: about_jimmy.txt and project_summary.txt updated for SambaNova; jimmy_background KB re-ingested (index 2,027 chunks)
- Next (Phase 9): index format optimization (JSON→NumPy), ingest path normalization, response streaming, curated content narrative rewrite

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
