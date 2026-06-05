# Disposable Email Domains — Public Feed

Daily-refreshed lists of **disposable / temporary email domains**, **free
webmail providers**, and **excluded legitimate domains**. Pull as raw text
over HTTPS and ingest however you like.

This repo is the **publish surface** only — it contains nothing but the
three feed files and this README. The classifier, training pipeline, and
discovery infrastructure live in a separate private repository and
publish here daily.

## Stable URLs

```
https://raw.githubusercontent.com/whois-api-llc/disposable-email-domains-feed/main/data/disposable.txt
https://raw.githubusercontent.com/whois-api-llc/disposable-email-domains-feed/main/data/free.txt
https://raw.githubusercontent.com/whois-api-llc/disposable-email-domains-feed/main/data/excluded.txt
```

Each file is newline-delimited, sorted, lowercase, one apex domain per
line. Refresh cadence: **06:00 UTC daily** (auto-commit when the
upstream consensus changes — no empty commits, no churn on quiet days).

| File | Approx. size | Purpose |
|---|---:|---|
| `data/disposable.txt` | ~160,000 | Domains classified as disposable / temporary email providers. |
| `data/free.txt` | ~5,000 | Free webmail providers (Gmail-class). |
| `data/excluded.txt` | ~230 | Legitimate domains that have appeared on community block-lists but are confirmed never disposable. |

## How the feed is built

The upstream classifier combines:

- **Community block-list consensus** across 7 disposable sources and
  3+ free sources.
- **MX-infrastructure clustering** — 369 disposable mail-server clusters
  identified via live DNS resolution.
- **Reverse-MX expansion** — domains discovered by reverse-MX lookup
  against known disposable clusters, including ~45,000 entries absent
  from any public block-list.
- **XGBoost classifier** — final per-domain disposable/free decision
  with confidence scores. Validated at 99.27% precision / 97.25% recall
  on a held-out test set.

This repo only publishes the final three text files. The full enriched
JSON output (with confidence scores and infrastructure metadata) is
available via the WhoisXML API
[Disposable Email Domains data feed](https://emailverification.whoisxmlapi.com/disposable-email-domains).

## Versioning

The text format is **append-mostly** — new disposable domains are added
as discovered; entries are removed only when they're confirmed
legitimate (and moved to `excluded.txt`). Consumers should not rely on
position or order — sort order is the only stable property.

## License & attribution

The disposable-domain detection methodology and ML classifier are
WhoisXML API original work. Community block-list sources are credited
in the upstream repository.

## Issues

Wrong classification, missing domain, false positive? Open an issue on
this repo — we monitor and route to the classifier team.
