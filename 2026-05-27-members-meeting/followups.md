# Members Meeting 2026-05-27 — Follow-ups

Things promised during the [2026-05-27 Members Meeting](./transcript.md): questions where Jason said he'd check on something, and small fixes / feature requests where the answer was "yes, we'll do that."

For larger items, this file links out to the dedicated tracking issue or oxjob.

## Promised answers

Questions where the answer was "let me check and get back to you."

| Asked by | Question | Status | Answer / link |
|----------|----------|--------|---------------|
| Tobias Jeppsson (KTH), seconded by Gloria Gonzalez (EBSCO) & Shylaja L | Decreasing abstract coverage from some publishers — is this a real trend, and what's being done about it? | answered — emailed to all three 2026-05-30 | Yes, it's real. We pulled the numbers from our own data and built an interactive report: **https://oxjobs.org/reports/276** (coverage over time, by publisher, with an OA-vs-closed toggle). Of the top 50 publishers, 12 show a clear year-over-year decline — but that count *understates* the problem: the biggest publishers (Elsevier, Springer Nature) don't read as "declining" because we dropped all their toll-access abstracts in one step, so their line is flat-and-low rather than trending down. Next: scoping how much our landing-page parsing work recovers. |
| Shylaja L (Informatics) | License metadata mismatch — e.g. `10.1515/chem-2017-0001`: OpenAlex says CC-BY but the article is BY-NC-ND 3.0. | confirmed — fix scheduled | **Confirmed real, and systematic.** We checked the article against Crossref (the publisher's own registered metadata): it really is BY-NC-ND 3.0, and OpenAlex was wrong. Not a one-off either — every recent article we sampled from that journal was mislabeled the same way. Root cause: for fully-open-access (DOAJ) journals our pipeline stamped the journal's default CC-BY onto each article, overwriting the article's real license. Now a tracked data-pipeline fix, assigned to our CTO. |

## Quick fixes & feature requests

Small bugs / features promised on the call. Larger ones get their own oxjob — link here.

| Item | Status | Owner | Link (PR / oxjob / ticket) |
|------|--------|-------|----------------------------|
| **Bug:** non-article OA links — an OA book *chapter* appears to flag the whole *book* as OA (Jeremy Prevost, seconded by Shylaja L) | likely fixed | — | A known reported instance (a Palgrave book) now reports correctly as closed-access, so this appears already fixed in the current data. No specific example was given on the call — if you hit one that's still wrong, send the work and we'll reopen. |
| Country-scoped affiliation linking/unlinking in the org curation tool — let an org say "all `KTH`-from-Sweden strings → KTH Royal Inst of Tech" (Tobias Jeppsson) | not planned for now | — | Agreed this is valuable but hard to do well; we're not taking it on yet. The related, smaller piece (preview/export the affected works before a bulk link) is being built — see next row. |
| Export the set of papers under a given affiliation before bulk link/unlink (not possible in the curation UI today) (Tobias Jeppsson) | tracked | — | Building it — internal job filed. We'll add a way to preview/export the exact set of works a bulk link/unlink would touch before you commit. |
| Add users from outside your email domain to the org dashboard, for outside partners (Maria Praetzellis) | tracked | — | Building it — internal job filed. We'll add an admin override to grant access to partners on a different email domain (with sensible guardrails). We'll ping you when it ships. |
| Collections available via the API (Maria Praetzellis) | open | — | Collections just launched; roadmap |
| Expose first-name + last-name from the new author-name recognition in the API (Tijmen Altena) | parked | — | Jason: maybe, pending demand — gauging interest |

## Larger requests (parked / gathering interest)

Not promised on the call, but worth tracking — things that came up and were deferred pending more community signal or funding.

| Item | Notes |
|------|-------|
| **Curator certification program** — esp. reduced-cost, possibly partnered with LIS grad programs | Strong interest: Eric Jeangirard, Hayley Reeve, Tijmen Altena; Gloria Gonzalez (EBSCO) offered to sponsor certifications for new librarians. Biggest single theme of the Q&A. |
| **Affiliation matching at the source** — fix the underlying algorithm (e.g. bare "KTH" mis-mapping to Khyber Teaching Hospital), not just case-by-case (Tobias Jeppsson; related: Komi Agblodoe on correcting wrong institution assignments) | Partly addressed: the org affiliation editor changes matching going forward (Kyle shared a training video). Deeper ask = systematic algorithm fixes + country-based rules. |
| **Raw acknowledgement / full funder string field** (Tobias Jeppsson, seconded by Julien Chevrier & Gloria Gonzalez) | Today we only expose a *mapped* funder field; most actual funders on a paper are missing. **Tracked → internal job filed** (backlog, low urgency). We'll preserve/expose the verbatim acknowledgement/funder string; we'll follow up when it ships. |
| **ANR ↔ HAL funding data** — HAL returns ~7× the ANR-funded works OpenAlex does (curated authority pipeline) (Kumar Guha) | Kyle: "we've been talking to ANR and are keen to fully leverage their data." Kumar offered to send the comparison results. |
| **Funding-consortium monitoring via Collections** — a collection of funders → "works funded by any of these" (Adam Buttrick) | Kyle: "yes, that will be possible, but isn't yet." |
| **EBSCO journal-collections collaboration** — share libraries' subscribed-journal lists to seed Collections (Gloria Gonzalez) | Kyle: "that's the exact use case that started this feature — happy to follow up afterwards." |
| **Comprehensive OpenAlex API "skill"** covering all API functionality (preferred over MCP) (Adam Buttrick; +Jeremy Prevost, Eric Jeangirard, Trang Le) | DevRel: package the API as an agent skill. |
| **MCP server feedback** — members offered to test the in-development MCP server (Markus Skyttner) | **Tracked — internal job filed.** We'll ping Markus (and others who're keen on agent access, e.g. Adam) for feedback once the MCP server hits beta. |
| **Publication-year inconsistencies vs Scopus** (Maria Mora Mora) | Offered to share the stats + affected works she validated against Scopus + raw data. Invite the data. |
| **AER (entity resolution) v4 release estimate** (Maria Mora Mora) | Answered on the call: this *is* v4 — it's not a one-time release but continuous, incremental improvement of the current algorithm (e.g. re-sourcing ORCID after fixing error-propagation bugs), not a big-bang rerun from scratch. No single dated release to estimate. |

---

**Status legend:** `open` · `in-progress` · `answered` · `done` · `wontfix`
