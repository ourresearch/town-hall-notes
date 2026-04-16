> **How this document was generated:** On 2026-04-08, we gave the following prompt to [Claude Code](https://claude.ai/claude-code) (Claude Opus 4.6) with access to our [oxjobs](https://github.com/ourresearch/oxjobs) repo, the [OpenAlex blog](https://blog.openalex.org), and git history for [openalex-gui](https://github.com/ourresearch/openalex-gui), [openalex-users-api](https://github.com/ourresearch/openalex-users-api), and [openalex-walden](https://github.com/ourresearch/openalex-walden). All of these are open source — you can run a similar prompt yourself and get comparable results.
>
> *"The OpenAlex community advisory board (CAB) is meeting tomorrow. I need to give a little talk about the things that we've done over the last three months. I'd like you to comb through oxjobs and the OpenAlex blog and maybe the commit history for the GUI and for the users API and for `openalex-walden`. Help me prepare a short description to brag about all the good things we've done. Create it as a Markdown document. It should be well organized and maybe a page or two long. To help you get started, a few of the things that I know we did are we did the Alice launch, where Alice was the code name for the new usage-based pricing, and I think that also came with a number of new features like semantic search. And then also we made a bunch of data quality improvements for repositories, for affiliations, for abstracts, for FWCI (field weighted citation index), and for topics. We also added a bunch of content from IRDB, the Japanese repository aggregator. But those are just the things that I can think of right now. I'd like you to find other things that maybe I forgot and also flesh those ones out. Basically, we just want to brag to the community that we got a lot done."*

# OpenAlex Q2 2026 Update — Community Advisory Board

*Q2 2026*

## Alice: New Features and Usage-Based Pricing

In February we shipped **Alice**, our biggest release in over a year. The highlights:

- **Semantic search (beta):** Search by meaning, not just keywords. We built a custom Elasticsearch vector index with 413M embeddings — including 197M title-only embeddings so even works without abstracts are searchable. Queries run in under 250ms.
- **Advanced search:** Proximity search, exact matching, wildcards, and long queries up to 8KB. A complete rewrite of our search infrastructure.
- **Content API:** Direct access to 60M+ open-access PDFs and GROBID TEI XML files via simple URLs like `content.openalex.org/works/{id}.pdf`. We also published a Parquet manifest (62M rows) for bulk sync.
- **Usage-based pricing:** Replaced blunt rate limits with a transparent credit system priced in dollars. Free tier ($0.01/day) for individuals, plus Pro, Max, Member, and Partner tiers for heavier use. One-time credit purchases via Stripe for burst usage.
- **Completely rebuilt documentation** at developers.openalex.org.

## Data Quality Improvements

We fixed tens of millions of records across several dimensions:

- **Repository source attribution:** Fixed 44M works that were misattributed to the wrong repository source. Created 797 new source records and corrected 1,035 existing ones. Also fixed `any_repository_has_fulltext`, which had never actually been implemented — it now correctly flags 163M works.
- **Affiliations:** Backfilled 23.9M affiliation strings that were missing from our lookup table, restoring institution IDs for ~20M works. Applied ~165K corrections from the French Ministry's Works-Magnet tool. Diagnosed and partially fixed lower institutional coverage for 2024–2025 works.
- **Abstracts:** Fixed a pipeline gap where ~3M landing-page abstracts (2024–2026) were extracted but never made it into the API. Re-ran topic and SDG models on 1.27M works that gained abstracts. Also fixed `has_abstract` which was incorrectly set for ~22M works.
- **FWCI:** Replaced a stale external lookup table with an inline calculation that guarantees average FWCI = 1.0 within any cohort. Fixed `is_in_top_1_percent` to be derived directly from citation percentiles.
- **Topics:** Unified the topics pipeline and cleared a 17.9M-work backlog. Re-ran topic inference on 7.3M works that gained abstracts.

## Funder & Awards Metadata Expansion

Backed by a **$3.6M grant from Wellcome**, we massively expanded our funding metadata. Awards are now first-class objects in the OpenAlex graph, and we ingested grant data from **30+ research funders worldwide**, including:

- **US:** NIH, NSF, DOE, DOD
- **UK:** UKRI (all Research Councils), NIHR, Wellcome Trust
- **EU:** ERC, European Commission, DFG, ANR, AEI, FWF, SNSF
- **Canada:** NSERC, SSHRC, CIHR, CFI
- **Asia-Pacific:** KAKEN/JSPS (873K grants), ARC, NHMRC
- **Latin America:** FAPESP, ANID/FONDECYT
- **Nordics:** Swedish Research Council, Formas, Forte, Vinnova
- Plus Gates Foundation, NWO, and dozens more in progress

We also built a standardized 8-step ingestion workflow and funder tracker to systematize ongoing additions.

## Japanese Repository Integration (IRDB)

We completed a contract deliverable to ingest **~4.6M records from IRDB** (Japan's Institutional Repository DataBase) via a centralized OAI-PMH endpoint using the JPCOAR 2.0 metadata format. This replaced 74 failing individual NII endpoints. We also created Source records and endpoint mappings for ~700 Japanese institutional repositories, and added ~500 new OAI-PMH endpoints from OpenAIRE's registry.

## Author Disambiguation (AER v4 Foundations)

We laid the groundwork for a next-generation author disambiguation system:

- Built a **Python name parser** achieving 89–93% accuracy on a 15K gold standard (produced by Opus).
- Computed **106.7M author-level embeddings** and **718M per-authorship similarity scores** for merge/split detection.
- Split **~3.2M overmerged author profiles** using raw ORCID conflicts.
- Built population-based **name frequency priors** from external data for better merge decisions.
- Fixed an author-sequence bug corrupting author ordering across provenances.

## Affiliation Curation

We shipped a complete **community-driven affiliation curation system**, initially available to Member institutions:

- Match/unmatch interface for linking raw affiliation strings to ROR records.
- Role-based access: org-level curators and site-wide curators for trusted community members.
- Status tracking so curators can see when their corrections have been applied.
- Curations propagate to the API in the nightly pipeline.

## GUI Modernization

The web interface got a major overhaul:

- **Novice/Expert modes:** A simplified Google Scholar-like view for new users, with one-click toggle to the full expert interface.
- **All 21 entity types** now have first-class search, browse, zoom drawers, and CSV export (was only 6).
- **Sidebar navigation** replacing the old top bar.
- **Repository dashboard** for repository operators to monitor their integration.
- **Accessibility audit:** Vuetify upgrade and WCAG 2.1 compliance work.
- **New landing page** positioning OpenAlex as "the universal research database — built for agents, scripts, and spreadsheets."

## Daily Snapshots

We built a **daily incremental snapshot pipeline** so consumers can stay in sync without waiting for monthly full downloads. It covers all 21 entity types in JSONL and Parquet formats, with hash-based change detection so only records that actually changed are exported.

## OECD FORD Taxonomy Mapping

In collaboration with **NORA** (Danish National Open Research Analytics), we mapped OpenAlex subfields to the OECD Fields of Research and Development (FORD) taxonomy — covering 38 of 42 two-digit FORD fields. This supports the upcoming Danish Research Portal and enables standardized research analytics worldwide.

## Infrastructure & Reliability

- **$135K/year in savings:** Shut down legacy Heroku apps, migrated Unpaywall to RDS, and consolidated infrastructure.
- **Public status page** at status.openalex.org with automated alerts and post-deploy smoke tests.
- **CI/CD pipeline** for Databricks via GitHub Actions, preventing accidental job deletion.
- **POSI v2 recommitment:** Published a public self-assessment against all Principles of Open Scholarly Infrastructure.

## Community Growth

- Launched the **Member tier** ($5K/year) with admin dashboard, affiliation editor, Unsub access, and CAB nomination rights. University of Victoria was first; joined by Université de Montréal, KISTI, University of Queensland, and Statistics Denmark.
- Published **6 knowledge base articles** at help.openalex.org.
- Hosted a **"Vibe Coding with OpenAlex" webinar** for beginners.
- **Three-day author disambiguation hackathon** in March, with a call for community gold-standard datasets.
