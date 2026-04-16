# Project Context — jimmyhubbard2.cc Portfolio
Last updated: 2026-04-16

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
| RAG Knowledge Chatbot | https://d1r1qv7io7k8vk.cloudfront.net | Live, HTTPS ✅ (Phase 5 complete) |

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
- API Endpoint: `https://uiauqskgv0.execute-api.us-east-1.amazonaws.com/dev/query` (live — Nebius Llama 3.3)
- Frontend URL (CloudFront): `https://d1r1qv7io7k8vk.cloudfront.net` (distribution EN88LEBW14923)
- Frontend URL (S3 raw): `http://rag-chatbot-603509861186-dev.s3-website-us-east-1.amazonaws.com/frontend/index.html`
- Ingest Lambda: `rag-chatbot-ingest-dev` (deployed, tested, confirmed working)
- Query Lambda: `rag-chatbot-query-dev` (deployed, live, multi-provider router)
- Titan Embeddings v2: ✅ UNBLOCKED — confirmed live 2026-04-14
- Bedrock generation: ✅ Model migrated to `us.anthropic.claude-haiku-4-5-20251001-v1:0` (cross-region inference profile); smoke test pending marketplace propagation
- Nebius Llama 3.3-70B: ✅ LIVE generation path
- SSM: Nebius API key stored at /rag-chatbot/nebius-api-key
- GitHub repo: jhubb88/aws-rag-chatbot
- Tags: v0.3-ingest, v0.4-query, v0.5-frontend, v0.6-retrieval-tuning, v0.7-observability
- Chunk size: 175 words (down from 500); index has 43 chunks
- Curated knowledge files: data/curated/ (project_index.txt, project_summary.txt, work_history.txt, work_history_index.txt)
- Top retrieval scores: project queries 0.4179, employment queries 0.4047–0.4820
- CloudWatch dashboard: `RAG-Chatbot-Dashboard` (4 widgets)
- CloudWatch alarms: `rag-chatbot-error-rate-dev` (>5%), `rag-chatbot-p95-duration-dev` (>10s) — both OK
- SNS topic: `RAG-Chatbot-Alerts-dev` → jimmy.hubbard0813@gmail.com (confirm subscription email)

---

## In Progress / Next Steps

### RAG Knowledge Chatbot
- **Status: Phase 5 complete (v0.7) — CloudWatch, alarms, SNS, CloudFront, model migration**
- Next: Bedrock smoke test (marketplace propagation), then Phase 6 (integration testing)

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
