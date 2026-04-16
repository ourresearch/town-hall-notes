# Q2 2026 Roadmap

What we're planning to ship and improve over the next quarter.

## author accuracy

We've got great momentum on this from Q1. We'll keep pushing on it. It'll take about 6wks. Work packages:

- ~~basic gold standard~~
- ~~refactor the authors table in dbx~~ 
- ORCID
    - ~~split~~
    - merge
    - raw_orcid in works api
- Name parse
    - parse: ~~make algo~~ and check it
    - split on names,
    - join on names
- Curate
    - ~~frontend~~
    - users db and api
    - curations happen in databricks (core system)
- Ongoing algo (ORCID, etc)
- Fancy split algo (probably out of scope)

## Data quality

We pushed a lot of big stuff forward last quarter and we need to take some time to improve the smaller stuff: lots of data quality fixes, driven by Zendesk tickets. By its nature this work defies high-level summarization, but these are the *kinds* of things we're working on:

- fix many arxiv bugs (language, PMH locations, missing PDFs)
- abstract coverage
- missing institution.country_code values
- publication_year > 2026 shoudl be null
- add crosswalks for innumerable ideosyncratic repository work.type taxonomies


## Process

- semiautomated ticket solving
- further oxjob development (homegrown issue tracker)
- data quality and process QA dashboards

## Community

- London funder workshop
