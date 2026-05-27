> **How this document was generated:** On 2026-05-27, we gave the following prompt to [Claude Code](https://claude.ai/claude-code) (Claude Opus 4.7) with access to our [oxjobs](https://github.com/ourresearch/oxjobs) repo, the [OpenAlex blog](https://blog.openalex.org), and git history for [openalex-walden](https://github.com/ourresearch/openalex-walden), [openalex-gui](https://github.com/ourresearch/openalex-gui), [openalex-users-api](https://github.com/ourresearch/openalex-users-api), [openalex-elastic-api](https://github.com/ourresearch/openalex-elastic-api), [openalex-api-proxy](https://github.com/ourresearch/openalex-api-proxy), and [docs](https://github.com/ourresearch/docs). All of these are open source — you can run a similar prompt yourself and get comparable results.
>
> *"The first-ever OpenAlex Members Meeting is today. I'd like a quick recap of what we've shipped since the Q2 town hall on May 1 — about four weeks of work. Please comb through oxjobs (jobs completed since 2026-05-01), the OpenAlex blog, and git history across our public repos. Organize it for paying Member-tier institutions. Be concrete with numbers when we have them, but keep it to about a page."*

# OpenAlex Members Meeting — May 2026

*A four-week update since the [Q2 2026 town hall](https://www.youtube.com/watch?v=SfbXmKieAYg) on May 1.*

## Author profile claim + curation (the headline)

The biggest user-facing surface to land this month is the new author profile workflow. Members have been asking for this for years.

- **Claim your author profile.** Every author page now has a public "Claim" button. Signed-in users provide evidence; claims auto-approve for trusted academic/government email domains via the new `is_trusted_email_domain()` check (backed by JetBrains/swot — catches ETH Zürich, Max Planck, CNRS, RIKEN, and ~30–40% of international research institutions that the prior regex missed). Unclaimed-but-pending profiles get a gold "Claim pending" badge visible to everyone. A new `/admin/author-claims` dashboard handles moderation.
- **Curate your own profile.** Once you've claimed, you can edit your `display_name` / `full_name` and add or remove works from your profile directly in the GUI. There's a 1,000-add/day cap to keep things sane.
- **Generalized curations dashboard.** The dashboard now handles all curation types (raw affiliation strings, author profiles, individual works) in one place, with a new `previous_value` snapshot column so reviewers can see "before → after."
- **Legacy backfill.** We replayed an old Google Form that had been collecting author corrections for two years: **801 form rows became ~10,300 curations** through the new API, sample-verified live.
- **Security hardening.** Submitted curations whose desired state already matches OpenAlex are now rejected with a 409 (no more junk pending rows). The `user_id` field on `POST /curations` is server-set and can't be spoofed from the request body — we caught and reassigned 1,466 rows that had been submitted under impersonated Charité accounts.

For Member institutions specifically: this is the foundation for the affiliation-curation work we previewed in February, now extended to authors. Site-wide and org-level curator roles work the same way.

## Multi-email accounts

Academics often sign up with a Gmail address and only later add their institutional email — and historically that meant they couldn't be counted under their organization's premium plan.

- Users can now hold multiple verified email addresses (Linear-style), with the primary email driving org affiliation.
- Deleting a primary auto-promotes the next verified address.
- Auto-org-join now happens only after the magic-link click, not at signup — which has the side effect of hiding system-internal `orphan-key-*` rows from the members tab on org pages.

This directly addresses the biggest churn risk for member-institution rosters.

## Awards graph filled out

We're now state-of-the-art on funder/awards coverage (per [Kramer & de Jonge, March 2026](https://bmkramer.github.io/SesameOpenScience_site/thought/202603_open_funding_information/)) and this month we made awards a useful first-class entity.

- **Topics on awards.** Our topic classifier now runs on awards: `primary_topic` plus top-3 topics on **~3.46M** awards. Awards are filterable and group-by-able by topic, subfield, field, and domain.
- **Institutions on awards.** A new `institution_awarded` field links grants to ROR/OpenAlex institutions via our existing disambiguation pipeline, with per-source score thresholds.
- **USD-normalized amounts.** Awards now carry an `amount_usd` field computed from IMF/World Bank annual FX rates, so cross-funder award amounts are comparable for the first time.
- **13 new funder pipelines** (priorities 37–49): IDRC, Templeton, Rockefeller, Arnold Ventures, Nobel Prize, Argentina MINCYT, HHMI, and more — adding ~70K new awards across 16 funders. The walden tree picked up roughly 60 funder/prize source pipelines across the month (Nobel, Wolf, Lasker, Kavli, Fields Medal, Kyoto, Balzan, Holberg, Brain Prize, King Faisal, Breakthrough, Blavatnik, Japan Prize, Stockholm Water, MacArthur Fellows, plus 14 US federal funders via USAspending).
- **Fixes.** A 12.9M-row "is this funder-award-id actually English text" pass removed generic-word ghost awards — one bad ID ("China"/NNSFC) had been claiming 1,084,733 works. UKRI Gateway-to-Research got its grant amounts back: **169,428 of 170,797 records** (99.2%) had been NULL. DataCite-sourced amounts and currencies now parse instead of being hard-coded NULL.
- **Awards index v4.** The new ES index ships these fields with `_full` denormalized siblings so filtering and group_by stay fast.

## Content API quality (huge cleanup)

If you use the Content API for cached PDFs and GROBID TEI XML, this month was the cleanup pass.

- **GROBID XML error rate dropped from 17.80% → 0.88%** — a 20× improvement. We removed **4.45M** bad XMLs, added **5.16M** good ones (net +710K usable XMLs), and added safety guards that reject bad-cohort GROBID files at write and stream.
- **Closed the D1 ↔ ES sync gap** that had silently broken parts of the Content API since 2026-01-21; cleared the back-catalog corruption. Backfilled **5,158,475 D1 rows** missing `grobid_uuid`, which was the single biggest cause of Content API 404s.
- **Snapshot AWS sync.** New paid `/snapshots/credentials` endpoint hands out AWS STS credentials so subscribers can `aws s3 sync` the full ~700GB monthly snapshot using standard S3 tooling. We also corrected the docs — the quarterly snapshot is free; monthly is paid.

## Search reliability sweep

We took on a coordinated sweep of seven distinct power-user search complaints (UCL/EPPI, PIK, IBM, Ibict, Jean Monnet University) and closed all of them.

- **JATS-tag-in-abstract bug.** Crossref-sourced abstracts sometimes embed JATS XML tags as literal tokens, which were silently breaking phrase searches. We stripped them at ingest and reindexed: **33.1M JATS-bearing abstracts → 2,363** (99.993% reduction). One customer's Boolean query went from 1,811 → 50,754 results.
- **Long Boolean queries no longer 500.** Long `search=` queries were overflowing Elasticsearch's `http.max_initial_line_length` because the `preference` parameter (used for shard affinity) was being sent as the raw query string in the URL. We hash it now. The proxy returns a structured JSON 400 if a request URL still exceeds the cap, and the docs document the limit.
- **Curly-quote normalization.** Curly quotes (` ” `, ` ‘ `) now normalize to straight quotes in search input. Closes Zendesk #8592.
- **GUI proximity/Boolean regression fix.** The GUI was double-wrapping `.search` filter values in quotes since March, which produced silent wrong-counts and 500s on some proximity/Boolean queries. Fixed.

## Collections (saved entity groupings)

Shipped publicly 2026-05-26 after 11+ phases of design and rename gymnastics (collections → labels → collections again — sorry).

- Create, save, and share custom groupings of any entity type (works, authors, sources).
- Public collection pages with a "Copy to my collections" button.
- New `?filter=collection:col_xxx` filter live in the API, with capped intersected-ID semantics.
- `/settings/collections` + `/admin/collections` dashboards.
- New [Mintlify docs page](https://developers.openalex.org).

This replaces the manual external-spreadsheet workflow members were using to track curated reading lists, evidence-synthesis corpora, and institutional publication sets.

## Author entity resolution (AER v4)

Continued from Q2 — the foundations laid then, the data overhaul shipped this month.

- **Split overmerged profiles** using the new Python name parser. Phase 2 (different first names, same last name) covered ~**20.9M works** across ~**1.2M authors**.
- **Raw ORCID in authorships.** New `raw_orcid` field preserves the source-level ORCID separately from the matched-profile ORCID — Crossref now exposes **44.5M source ORCIDs**, DataCite **97.1M**. Closes a long-running ask from researchers who wanted to see the original claim.
- **AER v4 gold-standard corpus locked in** as the basis for all matching/splitting evaluation.
- **Pipeline refactor** — daily `UpdateWorkAuthors` (~30 min) split into affiliation-only refresh + dedicated `MatchAuthors`, so we only re-match where it actually matters.
- **Authors index bumped to v19.**

## SERP pagination unblocked

Three-year-old complaint: pagination was capped at 10 pages / 100 results on every SERP. Now removed — Google-style sliding window goes all the way up to the API's page-1000 limit. Resolves Zendesk #7927, #5050, #2397, #1612.

## GUI improvements

- **Retraction banner** now appears at the top of work pages for retracted works.
- **Sidebar metrics** restored on `/works/W…` pages: FWCI, cited-by, cites, related-to.
- **10-year works + citations sparkline** on every entity page.
- **Gmail-style selectable lists** with row checkboxes and a tristate master checkbox on every SERP and entity-page works list. (Hidden behind a feature flag except on author-owner profiles for now.)
- **Streamlined entity page header/metadata/sidebar/works** section across non-work entity pages.

## Signup hardening + email deliverability

Two ugly underlying problems caught up with us this month, both now fixed.

- **Mailgun → Postmark migration for transactional email.** We discovered Mailgun had `ourresearch.org` on silent probation: 100 msg/hr cap, ~50% rejections during peak. We migrated transactional sends to Postmark, persisted message-ids in the DB for forensic debugging, and added bounce webhooks plus Slack alerts. **1,502 affected users had their magic links backfilled.**
- **Signup abuse defense.** New email validation chain at signup: HTML5 syntax + TLD requirement, RFC 2606 reserved-domain rejection, bundled `disposable-email-domains` list (~3,500 domains), MX record check, Cloudflare Turnstile, IP rate limit (3/IP/day), and `verified_at` is no longer pre-stamped — no free daily credit until the magic-link click. Soft-disabled **~2,575 existing bot accounts**. Bot waves had been costing roughly $1/account/day in free credits.

## Repository data quality

- **#156 Fixed misattribution of ~17M repo-provenance works.** Aggregator endpoints (SHILAP "lepidopterology" had been wrongly tagging 7M physics papers) now map to the right sources.
- **Per-repo funnel tracking** (#98) — for 25 sampled repos we now see ingest → scrape → PDF → landing-page → superlocations conversion. The first step toward systematically fixing the biggest repository ingest losses.

## Search-abuse / API reliability

- **Throttled toggle.** One-click "Throttled" toggle on admin user/org pages slows the offender to 1 req/sec via Cloudflare Durable Objects and surfaces an undismissible banner to them. Already used to recover from one identified abuse incident.
- **Root-caused the 2026-05-05 API slowdown** (5,500-character OR-of-phrases LLM queries averaging 14.5s/request, 20% 5xx) and shipped the long-search fix that prevents the same shape from causing future incidents.
