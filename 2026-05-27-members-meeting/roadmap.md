# Roadmap — what's on the table

**Through June: the [Q2 roadmap](../2026-q2/roadmap.md) is unchanged.** Author accuracy, data-quality grind, process improvements, London funder workshop. Nothing new to share there — we're heads-down executing.

**The rest of this doc is about Q3** (July onward). None of this is committed — it'll be set at the next quarterly town hall. **This meeting is your chance to push us in or out on each of these.**

---

## Collections — full version (~set)

Today's collections feature is limited: you can only filter within a single entity type. We're planning to fix that. Use cases this unlocks:

- **Library subscriptions.** Collect every Elsevier journal your library subscribes to → filter works in those journals over the last 2 years → see citation rates, OA %, and which works have authors from your institution.
- **Department analytics.** Collect the 50 authors in your chemistry department → see their output over time → compare to chemistry departments at peer universities.
- **Public / shared collections.** Consortium-level collections, vetted reading lists, public benchmark sets.

Pricing as we're thinking about it: **base collections stay free, advanced collections become a Member-tier feature.** This is part of a larger pricing rethink (see next item).

**For discussion:** What collections would you want to build? What's the biggest analytics question your institution currently can't easily answer with OpenAlex?

## OpenAlex Curation Certification (open for input)

Members keep asking for the ability to curate other people's profiles — your researchers, your affiliations, your journal records. We've held back because in past experiments untrained curators made high volumes of mistakes that cost us significant moderator time.

The idea on the table:

- **Train and certify individual curators.** Online course + test. Likely around $100, possibly more (we'd do the math). Scholarships available. Members get a number of free slots per year.
- **One certification or several?** Probably a set: OA status, author profiles, affiliation strings, journal metadata, etc. Open question.

The bigger strategic shift, also on the table:

- **Replace "curation rights" with "advanced collections" as the primary Member-tier value driver.**
- Today: $5K Member tier required to curate.
- Possible: anyone certified curates for a few hundred bucks; Members pay for advanced collections instead.
- This responds to feedback that $5K-to-curate feels like we're ransoming members' data. We hear that. The fee reflects real moderation + engineering costs, but tying it to collections instead of curation may be a fairer fit.

**For discussion:** Would your institution prefer this model? Would you send curators through certification? What price would actually work for you?

## Better tools for user ↔ org assignment

A May win was multi-email signup: your researchers can add their `institution.edu` email to a Gmail-signup account and get counted under your org. That's a start.

What we're thinking about next:

- **Bulk roster import** for org admins (upload a CSV of researchers / ORCID iDs / emails).
- **Domain auto-association** improvements.
- **Manual admin override** — add a user to your org even if their email doesn't match.

**For discussion:** Which of these would move the needle for you? Anything we're missing?

## AI / agent access (MCP, CLI, the dashboard-by-agent vision)

We already have a CLI. An **MCP server is in development.**

**Where this is heading (a strong focus area for us — expect movement this month and ongoing):** members made clear they want great programmatic + agent access over an AI tool bolted onto the website, and Adam's idea of "a skill that covers all the API functionality" drew a lot of support. So our near-term plan is some combination (one or all) of: **improving the CLI**, **adding more recipes to the documentation**, **shipping an Anthropic-style Skill**, and **shipping an MCP server**. We think Skills and MCP serve two different audiences (data-analysts who point an agent at our docs vs. people living in Claude/ChatGPT), so it's likely not either/or.

The future we keep pointing at: most users will access OpenAlex through their own agents, not through our GUI. Examples this enables:

- *"Curate these author profiles for me."*
- *"Make a collection of the journals in this spreadsheet, then show me citation rates of my institution's authors in those journals compared to authors at peer institutions."*
- *"Build me a dashboard of last year's NIH-funded chemistry output at our institution."*

The agent does the API calls and renders a result tailored to your exact question. We don't have to build every possible UI affordance because every user's agent builds the one *they* need.

**The real tension:** many librarians have AI restrictions — institutional policy, ethical concerns, water/energy use, or simply not being permitted to use AI at work. We need to calibrate how fast we lean into this future against what members can actually use today.

**For discussion:** How are you (or aren't you) using AI with OpenAlex today? What's blocking you institutionally? Are agent-driven workflows something your stakeholders would adopt — or actively avoid?

---

## How we'd like to use this meeting

Each section above ends with a discussion prompt — those are real questions, not rhetorical. We're at the point in Q3 planning where member input meaningfully changes what we commit to. Tell us what's wrong, what's missing, and what you'd swap out for what.
