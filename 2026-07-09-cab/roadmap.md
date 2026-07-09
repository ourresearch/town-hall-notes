# Roadmap — what's next

What we're actively working on and planning for the rest of Q3. As always with the CAB: **none of this is set in stone — this meeting is your chance to push us in or out on each of these.**

## OQL: alpha → beta → launch

The advanced query language and visual builder are in alpha with hand-picked external testers now. The path: polish the docs (the hard gate for beta), widen the beta, then public launch. Also in flight: a redesigned no-code builder layout, saved column views, and opaque short share-URLs for queries.

**For discussion:** Who at your institution should be in the beta? What would make this the tool your librarians and bibliometricians actually switch to?

## More support automation

The author-profile fixer proved the model: an AI agent doing real curation work through the same public APIs humans use, with human oversight and audit trails. Next:

- Hand the author-fixer to a standing human operator and keep it running on new tickets as they arrive.
- Extend the pattern to other ticket categories (affiliation corrections, source metadata, OA-status disputes).
- Fix the remaining curation-pipeline reliability issues (a batch of ~12K stuck affiliation curations is being cleared now).

**For discussion:** How do you feel about AI-assisted responses to your support tickets? What guardrails matter to you?

## Work-type classifier to production

The taxonomy and crosswalk fixes are live; the remaining piece is the interpretable rules-cascade classifier that sorts the ambiguous `article` bucket (accuracy 0.71 → 0.86 on our gold set). We'll productionize it carefully — work type changes ripple through everyone's analytics.

## Search infrastructure

The June incident response is done; the systematic program continues:

- Offload single-record lookups (which outnumber all searches ~3:1) from Elasticsearch to an edge cache + Postgres — a pilot is already serving lookups in ~2ms.
- Rebalance hot-node skew in the works index.
- Keep tuning the adaptive search-health controller so paid and GUI traffic never feels a bot storm.

## Author & institution data

- Finish corresponding-institution recall (the +54M-work backfill is live; final verification in progress).
- A new coauthor-overlap matching tier for author disambiguation — in testing it uniquely resolves 68% of currently-ambiguous cases.
- Backfill works for ~1,800 newly-added ROR institutions currently stuck at zero works.

## Funding graph

- Japan: KAKEN grant→work linkages are harvesting now; JST is next.
- Split 2.3M NIH awards to their true institutes and model funder hierarchies.
- Extend EuropePMC funder resolution beyond NIH funders.
- Integrate awards fully into the graph (awards ↔ authors ↔ topics).

## AI / agent access (continuing from the Members Meeting)

The MCP server, CLI improvements, and an agent Skill covering the full API remain a strong focus — members told us in May they want great programmatic and agent access more than an AI widget on the website. Expect concrete movement here this quarter.

**For discussion:** Has your institution's posture on AI tools shifted since May? What would you need to actually point an agent at OpenAlex?

---

## How we'd like to use this meeting

The discussion prompts above are real questions, not rhetorical. We're mid-quarter — CAB input now meaningfully changes what we commit to for the rest of Q3 and what we bring to the next town hall. Tell us what's wrong, what's missing, and what you'd swap out for what.
