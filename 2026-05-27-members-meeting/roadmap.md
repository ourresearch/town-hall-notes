# Members Meeting Roadmap — looking ahead

What we're working on between now and the next quarterly town hall. This is an in-progress snapshot — finer-grained than the Q2 roadmap we shared four weeks ago, with more detail on what each thread looks like *next week*.

## Author accuracy (finishing what Q2 started)

Most of Q2's roadmap was "finish author accuracy in 6 weeks." We're roughly halfway through that window and the major pieces are landing.

- **Apply author curations end-to-end** (#168). The producer half — claim a profile, edit display_name, add/remove works through the GUI — is live. The consumer half (a Walden notebook that takes the curation rows and writes them back into `openalex_works` overnight) is in flight. When it finishes, your in-GUI corrections will appear in the API within ~24 hours.
- **Merge overmerged-author splits using ORCID** (#145 stragglers). Phase 2 split shipped this month; the corresponding merge side is next.
- **ORCID-based merging** for incorrectly-split profiles is close — was on the Q2 roadmap and is the next piece in the AER v4 pipeline.
- **Surface "no-op" and "no-byline" curations** (#251) — today, some submitted curations sit "pending" for 7 days even though they could never apply (the work doesn't have the byline being edited). We're going to reject these at submit time with an actionable error so users aren't waiting on something that will never happen.
- **Raw affiliation strings as durable identifiers** (#235). We need RAS to be safely immutable so downstream curations can reference them. Active this week.

## Repository ingest cleanup

Casey is running a multi-week sweep through repository ingest losses. The Q2 retro flagged that ingest funnel-tracking was missing; now that we have it (#98), we can act on the numbers.

- **IRUA harvester fix** (#227) alone is expected to unblock ~5.74M dropped records across 18 repositories — including ~2.1M from IRDB (the Japanese aggregator we onboarded in Q1) and ~1.1M from QUT. Active now.
- **LaReferencia / Dialnet routing** (#158) addresses ~8.1M misrouted works.
- **Repository operator dashboard** improvements continue, building on what was shipped in Q1.

## Funder / awards

The Wellcome-funded funder push continues. Two big pieces this quarter:

- **DataCite funding metadata** (#125, #125.2). Crossref alone gave us strong coverage of journal articles; DataCite extends that into datasets, software, preprints, and the long tail of non-Crossref-DOI scholarship. The ingest pipeline is wired up; this is now about quality at scale.
- **NWO grant ↔ work linkages** (#244). NWO has a curated dataset of which works each grant funded — most funders don't. This is a test case for ingesting that kind of authoritative linkage and could become a template for other funders that have it.
- **London funder workshop** — Wellcome-funded; me and Kyle are bringing funders together to listen. Q2 roadmap goal, happening on schedule.

## Content API quality

This month we did the cleanup pass on GROBID XML. Next is closing the loop with the heaviest Content API users.

- **Quantify the Stanford Medicine fetch-failure remediation** (#242). They reported ~797K Content API fetch failures; #185 and #202 should have closed most of those. We're auditing what's actually fixed vs still failing so we can ship the remaining diff.
- **Parseland → openalex-walden integration improvements**. Two Elsevier parser iterations (#246, #247) and two Springer iterations (#262, #263) are in flight, each measured against the new parseland gold standard (#122, #131). The point isn't "ship a fix" — it's "ship a fix whose effect on the per-publisher failure rate we can prove."

## Search reliability & abuse defense

After the cluster of search-recall and 500-error reports we resolved this month, we're investing in the long-term defenses.

- **Systematic query-abuse detection** (#194). Today we identify abusive query patterns reactively, after they degrade latency. Next: detect-and-block at the proxy, before they hit Elasticsearch.
- **Curation timeout status hardening** (#198). We have a 7-day `timed_out` terminal status now, but ~2,430 RAS curations are currently in that state — we need to understand why and ship the remediation path before this becomes a persistent regression.

## Data quality grab-bag (Zendesk-driven)

Same posture as Q2: keep raking the lawn. We have ~2,300 open Zendesk tickets and the goal is to grind through them. Some examples currently in flight or queued:

- Diamond OA misclassification — Walden treats null APC as `$0`, inflating Diamond OA counts (in-progress, fix scoped)
- Various arxiv ingest bugs (language, PMH locations, missing PDFs)
- Author-sequence corruption on small numbers of works
- Idiosyncratic repository `work.type` taxonomies that need crosswalks
- Aggregator-endpoint source mapping (sequel to #156)

## Process & AI-assisted ops

This is the thread that was carried over from Q2 — and where I personally spend the most time.

- **Semi-automated ticket solving.** Most Zendesk tickets follow predictable shapes. We're building agent-assisted triage where Claude proposes a diagnosis + draft reply + suggested oxjob for a human to approve. Early results are encouraging — the bottleneck has shifted from "find the bug" to "approve the fix."
- **oxjobs continues to evolve.** Our internal tracker now has labels (replacing project IDs), an oxjobs.org HUD with multi-select filters, and is starting to act like a process-QA dashboard.
- **Agent-driven story testing.** Experimental: before shipping non-trivial features, an agent runs through scripted scenarios (claim a profile, edit a work, etc.) end-to-end in a browser and reports failures. Early days; one trial run on the collections feature.

## What this means for Member-tier institutions specifically

A subset of this work is most directly relevant to paying members:

- **Author curation end-to-end** — your researchers can claim and curate their own profiles, with corrections flowing into the API within a day.
- **Org affiliation via multi-email** — your researchers' Gmail-signup accounts can now be properly counted under your institution's plan.
- **Collections** — save and share curated entity sets across your team without leaving OpenAlex.
- **Affiliation-curation v2** — same matching dashboard, now with the curation status lifecycle (pending → applied / timed_out) so your curators know what's been ingested.
- **Snapshot AWS sync** — paid `aws s3 sync` against our monthly snapshot using standard S3 tooling.

What we'd most like to hear from members in this meeting:

- What's missing from the above that you'd prioritize?
- Which of these features are you actually using? Where do you wish they worked differently?
- What would make Member tier more valuable to your institution?
