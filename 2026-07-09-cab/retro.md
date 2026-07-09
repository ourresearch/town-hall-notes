> **How this document was generated:** On 2026-07-09, we gave the following prompt to [Claude Code](https://claude.ai/claude-code) (Claude Fable 5) with access to our [oxjobs](https://github.com/ourresearch/oxjobs) repo, the [OpenAlex blog](https://blog.openalex.org), and git history for [openalex-walden](https://github.com/ourresearch/openalex-walden), [openalex-gui](https://github.com/ourresearch/openalex-gui), [openalex-users-api](https://github.com/ourresearch/openalex-users-api), [openalex-elastic-api](https://github.com/ourresearch/openalex-elastic-api), [openalex-api-proxy](https://github.com/ourresearch/openalex-api-proxy), and [docs](https://github.com/ourresearch/docs). All of these are open source — you can run a similar prompt yourself and get comparable results.
>
> *"The OpenAlex Community Advisory Board meets today. Please prepare a recap of what we've shipped since the Members Meeting on May 27 — about six weeks of work. Comb through oxjobs (jobs completed since 2026-05-27), the OpenAlex blog, and git history across our public repos. Be concrete with numbers when we have them, and keep it to a couple of pages."*

# OpenAlex Community Advisory Board — July 2026

*A six-week update covering 2026-05-27 through 2026-07-09. For what shipped in May (author profile claiming + curation, collections, the awards graph, the Content API cleanup, and more), see the [Members Meeting retro](../2026-05-27-members-meeting/retro.md).*

## Corresponding authors: from coin flip to trustworthy (the headline)

Corresponding-author data is essential for analyzing publishing patterns and negotiating transformative agreements — and ours was quietly bad. The old pipeline defaulted "first author = corresponding" whenever evidence was missing, fabricating a corresponding author on **195.5M multi-author works (38% of the corpus)** — and that guess was wrong nearly half the time.

- **Precision jumped 0.60 → 0.92** (F1 0.72 → 0.90), measured against a new hand-checked gold standard. Wrong assignments dropped **~80%**.
- We removed the first-author fallback entirely, then recovered real corresponding authors by re-parsing landing pages across Springer, Nature, Elsevier, Wiley, OUP, SAGE, ACS, and Frontiers — Springer-family alone yielded **2.17M new corresponding authors** from 4.1M reparsed works.
- Net: **~8.2M new corresponding-author assignments**; **7M works** gained a corresponding author (+21% of multi-author works with the designation).
- As a follow-on, **corresponding *institution* coverage went from 15.7% → 26.2% of works (+54M works)**, via a precision-guarded fallback (96/100 on a hand-checked sample).

Full write-up on the blog: [A big improvement to our corresponding-author data](https://blog.openalex.org/a-big-improvement-to-our-corresponding-author-data/).

**This work was funded by the California Digital Library (CDL)** as part of our data-quality collaboration — thank you, CDL! It's a great example of a member funding an improvement that benefits everyone.

## Work types: taxonomy overhaul, ~40M records retyped

Work type (`article`, `preprint`, `dataset`, …) is one of our most-used and most-complained-about fields. This window we rebuilt it:

- **A canonical taxonomy of 25 work types** is now live on `/types` and in the docs — 19 rewritten definitions plus **6 new types**: book-review, conference-abstract, conference-paper, data-paper, software, software-paper.
- **DataCite crosswalk rewrite** fixed ~3.6M mis-typed DOIs (the largest single error: ~2.9M software records typed as "other").
- **Repository (Dublin Core) crosswalk rebuilt** from a classification of the top ~2,000 raw type strings: **40M repository location records changed type**.
- Combined visible impact since June 29: `other` −9.3M, dissertation +4.0M, conference-paper +2.8M (from zero), book +1.8M, preprint +1.3M, software +955K. Works whose primary location is a curated preprint repository are now typed `preprint`.
- In the lab: an interpretable rules-cascade classifier for the ambiguous `article` bucket — accuracy 0.71 → 0.86 on a 10K hand-labeled gold set — proven but not yet in production.

## Advanced search: OQL alpha is live *(live demo in today's meeting)*

Six weeks ago OQL (the OpenAlex Query Language) was a frozen spec. Today it's a working alpha: a human-readable query language plus a no-code visual builder, strictly more expressive than our classic filter URLs (arbitrary nested AND/OR/NOT, proximity search, column projection, multi-dimensional group-by).

- **Correctness first:** we replayed **10,000 real API URLs** through the legacy and new engines side by side — 98.4% in-scope parity with zero unexplained mismatches. A real librarian's 114-clause systematic-review boolean query round-trips byte-identically.
- **Proximity + wildcard search** (`"climate" within 3 words of "policy"`) brings us to parity with Web of Science / Scopus search syntax for evidence-synthesis work.
- A **natural-language → query** endpoint translates plain English to OQL at 86% accuracy on our eval set, for about a quarter of a cent per query.
- First **alpha invites went out July 6** to hand-picked systematic-review and bibliometrics practitioners. Everything is behind a feature flag in the new **Settings → Labs** experiments surface; beta and public launch follow as docs mature.

Jason will screenshare a few examples today.

## Search performance and reliability

Mid-June, full-text search degraded badly — 4–22 second responses and intermittent errors at peak. We fixed the incident and then built the systematic program so it doesn't recur:

- **Scaled the search cluster 24 → 32 data nodes** — all search terms back under 1.5s, zero errors, autocomplete sub-second.
- **Rebuilt the 511M-document works index**: 9.5TB → 4.6TB on disk (−52%), and the slow-tail p95 dropped from ~4.5s to 3.3s.
- A new adaptive **search-health controller** at the edge watches cluster health and throttles only anonymous works-search when things degrade — nightly batch-load storms went from 41 minutes of degradation to ~11, with zero manual intervention.
- To keep the free API sustainable, the **keyless** (no API key) daily budget dropped from $1.00 to $0.10 of query credit per day. Fewer than 1% of keyless users ever hit the new cap, but the change sheds about a third of total search load from anonymous scraping. Registering a free account keeps the 10× higher budget — and the API remains open, no key required.

## Support automation: an AI agent cleared the author-profile backlog

"Please fix my author profile" is our single biggest support category. We built an AI agent that reads the ticket, curates the profile through the same public curation API humans use (removing wrong works, adding missing ones, merging splinter profiles, fixing names), and replies to the researcher.

- It worked through the entire backlog: **584 actionable tickets processed, ~300 replies posted, zero errors, about $70 in total model cost.**
- A follow-up pass measured outcomes on every engaged ticket; genuinely-unresolved residue was ~10 tickets, which got human attention.
- A second agent now **auto-approves clear-cut author-profile claims** (it never auto-denies — uncertain cases go to a human digest), running in production every 15 minutes.
- Behind the scenes we also fixed the curation pipeline reliability issues these tickets kept exposing: 97% of long-stuck affiliation curations resolved, and a recurring author-name maintenance job that made **267K name fixes** in its first run (with an adversarial audit that caught and reverted the 0.8% it got wrong).

More automation of ticket management is coming — this is a deliberate direction, not a one-off.

## The funding graph keeps growing

Following May's awards push, two big new linkage sources came online:

- **EuropePMC funding data**: 12.9M grant→work linkages across 8M works — including ~5.6M closed-access works that Crossref can't reach. ~31% of the award entities are net-new.
- **DataCite fundingReferences**: 2.2M work↔funder links across 12K funders.
- **~127 new funder award pipelines** in six weeks (Sloan, Moore, Cancer Research UK, SFI, FWO, NSFC, Taiwan NSTC, TÜBİTAK, Hong Kong RGC, and a batch of 109 mental-health research funders), plus the American Heart Association publishing its awards openly through us: 10,255 awards totaling $1.96B.
- Cleanups: 156K awards wrongly blanket-labeled "NIH" re-attributed to their true agencies (CDC, SAMHSA, VA, …), and 320K grants un-hidden from a dedup bug.

For a taste of what this data can do, see the new blog case study: [The Hakai Institute, as seen by OpenAlex](https://blog.openalex.org/the-hakai-institute-as-seen-by-openalex/) — a full funder-impact analysis (1,496 verified works, 95% precision) built entirely on open data, fully reproducible.

## Other notable ships

- **Results table view + WYSIWYG CSV export** in the GUI: pick, reorder, and relabel columns Scopus-style (~72 exportable columns on works), and export exactly what you see.
- **Collections got cross-type filtering**: filter works by a *sources* collection (the "my library's subscribed journals" workflow), and every entity type is now collectible. Also fixed: collection exports that had always downloaded empty files.
- **Abstracts**: recovered ~1.84M Springer/Elsevier OA abstracts via a one-line pipeline fix; published a [public report on publisher abstract coverage](https://oxjobs.org/reports/276). Parser accuracy pushes: Wiley abstracts 86.5% → 92.9%, Wiley PDF-URL discovery 4% → 91%.
- **Snapshots**: now built **daily** (was monthly) in both gzip-JSONL and **Parquet**; the changefiles listing is free and keyless. Snapshot export runtime for works dropped 132 → 56 minutes.
- **Our author-name parser is now open source on PyPI** ([`whatsername`](https://pypi.org/project/whatsername/), 88.8% full-field accuracy on a 15K-name gold standard) — a direct answer to a Members Meeting request.
- **A first-class `corpus` selector** (core / expansion / all) replaces the confusing `is_xpac` filter for choosing between the curated 317M-work core corpus and the 510M-work expanded one.
- **~42 new institutional members** joined, including the Wellcome Trust, TU Delft, Curtin, and 11 institutions via the JISC consortium; we're positioned as the open replacement for the Jisc Publications Router, which retired July 1.
- **openalex.org/brand** is live (logos, colors, typography), and the website now sits behind bot protection — while api.openalex.org stays intentionally wide open as a public good.

## Team

- Shubhankar Tripathy, who joined us as a junior developer earlier this year, has moved on — it didn't work out.
- **Rohan Mantena converted from contractor to full-time this week.** He joined on contract this spring and has been doing excellent work across the data pipeline — we're thrilled to have him aboard permanently.

## Finances & sustainability

Short version: it's going really well.

- **We're on track against the five-year plan targets in our Arcadia grant** — the sustainability milestones we committed to are being hit.
- **Membership and supporter growth is strong**: ~42 new institutional members joined in just this six-week window (see above), alongside continued growth in supporters.
- **Enterprise revenue is growing**, driven especially by a wave of new enterprise contracts for PDF-archive sync — organizations syncing our full-text archive for their own infrastructure.
