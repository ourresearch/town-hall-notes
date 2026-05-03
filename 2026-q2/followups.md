# Q2 2026 Town Hall — Follow-ups

Things promised during the [Q2 2026 town hall](https://www.youtube.com/watch?v=SfbXmKieAYg): questions where Jason said he'd check on something, and small fixes / feature requests where the answer was "yes, we'll do that."

For larger items, this file links out to the dedicated tracking issue or oxjob.

## Promised answers

| Asked by | Question | Status | Answer / link |
|----------|----------|--------|---------------|
| Jacob Hill | The author-merge bug from March 10 (Jason Priem ↔ Bradley Hemminger) — has the specific case been resolved? | open | Underlying root cause (cross-author name propagation) addressed via watertight name boundaries. Full re-run of affected records expected by end of Q2. |
| Eric | Walden seems to classify many more works as Diamond OA than pre-Walden. Is Walden treating null APC as zero? | open | Investigation needed — confirm whether null APC is being coerced to zero in the Walden OA classifier. |
| Ed | Where can the community track changes for the author entity resolution work? | open | Primary repo: `openalex-walden`. Python name parser is in a separate repo — exact location to be confirmed and added to the retro / docs. |
| Ed | Will the gold standard for the name parser be made public? | open | Yes. Verify it is in the repo (or push it) and link from the docs. |
| Last questioner | Which embedding algorithm is used? | open | Whichever open-source embedding Databricks implements natively. To be named explicitly in the API docs and verified in the `openalex-walden` repo. |

## Quick fixes & feature requests

| Item | Status | Owner | Link |
|------|--------|-------|------|
| Add link to Bianca Kramer et al.'s analysis of awards-coverage (OpenAlex vs Scopus / Web of Science / Dimensions) into the Q1 retro. | open | jason | — |
| Document the embedding model used for author-level embeddings and semantic search in the API docs. | open | | — |
| Publish the AI-generated name-parser gold standard alongside the parser. | open | | — |
| Publish town hall transcripts to the [`town-hall-notes`](https://github.com/ourresearch/town-hall-notes) repo going forward (this file is the Q2 prototype). | done | jason | This commit |

## Larger requests (parked / gathering interest)

Not promised on the call, but worth tracking — these came up and were deferred pending more community signal or funding.

| Item | Notes |
|------|-------|
| Allow callers to supply their own embedding vector to semantic search. | Will build if multiple users request it; please email support@openalex.org to add your vote. |
| Raise the 50-result cap on semantic search. | Cost-bound (indexing is the expensive part). Open to revisiting if there is funded demand. |
| Bulk download of author-level embeddings. | Currently internal; size & cost make distribution non-trivial. |
| Federated mirrors of OpenAlex. | OpenAlex is open to financially supporting genuine mirror operators — please reach out. |

---

**Status legend:** `open` · `in-progress` · `done` · `wontfix`
