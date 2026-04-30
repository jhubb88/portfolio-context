# Portfolio Infrastructure

Cross-cutting AWS infra shared by all portfolio projects. App-specific details live in each project's directory; this file covers what spans projects.

**AWS account:** `603509861186` · **Region:** `us-east-1` · **CLI profile:** `portfolio-user`

---

## 1. Cost-allocation tagging

Every portfolio AWS resource carries a `Project=<value>` tag (capital P). Values are lowercase, hyphenated, and locked to this set:

```
fieldiq, ntcip-simulator, text-to-audio, log-analyzer, resume-matcher,
traffic-dashboard, rag-chatbot, writing-workspace, linux-ops-copilot
```

**Untagged on purpose** (infrastructure, not projects): `aws-sam-cli-managed-default-*`, `jimmy-cloudtrail-logs-*`, `jimmy-advanced-projects` (S3 + CF E1VZ0ELKDC3LN0 — landing page).

The centralized CF log bucket carries `Project=infrastructure` so it isn't untagged.

When adding a new resource, tag at creation:
- **Lambda / CloudFront / API Gateway:** `tag-resource` is merge-style — safe to call directly.
- **S3:** `put-bucket-tagging` *replaces* the full TagSet. Fetch existing tags, merge, then put. Pass the payload via `--tagging file://<json>` (CLI shorthand fails on quoted values).

> **Manual step (one-time):** Billing Console → Cost allocation tags → User-defined → activate `Project`. ~24h propagation before Cost Explorer can group by it.
>
> _Activated 2026-04-30. Cost Explorer grouping by `Project` should be live from ~2026-05-01._

---

## 2. CloudFront access logs

All 9 distributions write standard access logs to one centralized bucket with per-project prefixes.

**Bucket:** `jimmy-portfolio-cf-logs-kxe7hq` (us-east-1)
**CF `Logging.Bucket` hostname:** `jimmy-portfolio-cf-logs-kxe7hq.s3.amazonaws.com`
**Lifecycle:** objects expire after 90 days (`Filter: {Prefix: ""}`)
**Bucket settings:**
- Object Ownership: `BucketOwnerPreferred` (ACLs enabled — required for CF standard log delivery)
- Public Access Block: `BlockPublicAcls=false, IgnorePublicAcls=false, BlockPublicPolicy=true, RestrictPublicBuckets=true`
- Bucket ACL: owner + awslogsdelivery (`c4c1ede66af53448b93c283ce9448c4ba468c9432aa01d700d3878632f77d2d0`) FULL_CONTROL

| Distribution | Prefix |
|---|---|
| `E12Z80TRB0P2XR` (FieldIQ) | `fieldiq/` |
| `E2PEIMT1J3W4MO` (NTCIP Simulator) | `ntcip-simulator/` |
| `E3H0XAJDR3BQG1` (Resume Matcher) | `resume-matcher/` |
| `E1BM7FLW1T9GAM` (Text to Audio) | `text-to-audio/` |
| `E3Q8ZCVRAS854T` (Log Analyzer) | `log-analyzer/` |
| `E3H1V6C42HG9P1` (Traffic Dashboard) | `traffic-dashboard/` |
| `EN88LEBW14923` (RAG Chatbot) | `rag-chatbot/` |
| `E39D8FLKEFZ16I` (Linux Ops Copilot) | `linux-ops-copilot/` |
| `E1VZ0ELKDC3LN0` (Advanced Projects landing) | `advanced-projects/` |

Logs land 5–15 min after the first request. To peek:

```
aws s3 ls s3://jimmy-portfolio-cf-logs-kxe7hq/fieldiq/ --profile portfolio-user
```

**Updating an existing distribution's logging requires ETag optimistic locking:** GET config + ETag, modify only the `Logging` block, `update-distribution --if-match <ETag> --distribution-config file://<json>`. Retry once on `PreconditionFailed` / `InvalidIfMatchVersion`.

---

## 3. CloudWatch dashboard

**Name:** `Portfolio-Traffic-Overview`
**URL:** https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#dashboards:name=Portfolio-Traffic-Overview

Three rows, all 7-day window, 1-hour period:
1. **Lambda Invocations** (stacked) — one line per portfolio Lambda
2. **CloudFront Requests** (stacked) — one line per portfolio distribution (9 lines)
3. **Lambda Errors** (aggregate) — single sum across all portfolio Lambdas, computed via `FILL(eN, 0)` math expression so missing data doesn't null the line

CloudFront metrics live in `us-east-1` regardless of distribution; the metric dimension `Region` is fixed to `"Global"`. The widget's `region` property is `us-east-1`.

**Cost:** free (first 3 dashboards per account are free; this is the 2nd alongside `RAG-Chatbot-Dashboard`).

When adding a new portfolio Lambda or CloudFront distribution, append it here too — fetch the current body before editing:

```
aws cloudwatch get-dashboard --dashboard-name Portfolio-Traffic-Overview --profile portfolio-user
```

Empty until traffic flows. After enabling logs/metrics, hit a portfolio site and refresh ~5 min later.

---

## 4. AWS CLI profile

All portfolio commands use `--profile portfolio-user`. Verify with:

```
aws configure list --profile portfolio-user
```

Never fall back to the default profile.

---

_Last verified: 2026-04-30. When you add or rename portfolio resources, update this file._
