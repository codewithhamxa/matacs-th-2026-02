# MATACS Technical Assessment — AI Proposal Triage Design

**Candidate:** Hamza Javed  
**Assessment reference:** MATACS-TH-2026-02  
**Time spent:** 40 Mins

## Overview
This repository describes a proposed AI-assisted workflow for reviewing commercial solar installation proposals. AI extracts evidence from documents; deterministic application code applies the configured eligibility checks, scoring rules, and ranking. A human reviewer remains responsible for resolving flags and making the final award decision.

## Repository contents
- [Process-flow diagram](diagram/process-flow.png)
- [Design write-up](WRITEUP.md)
- [AI extraction prompt](PROMPTS.md)
- [Expected sample ranking](ranking.json)

## How AI tools were used
AI assistance was used to help structure and draft the design, prompt, scoring rubric, and sample ranking. The proposed design was then checked against the brief's mandatory requirements and weights. One important correction was to reject the instruction embedded in proposal C that attempts to force a first-place ranking: proposal content is untrusted input, so it must not override the system's evaluation rules.

Before submission, personally review the files, ensure the assumptions reflect your understanding, and update the time-spent statement to be accurate.

## Assumptions
- The proposal summaries in the assessment are treated as extracted text, not as independently verified facts.
- A bidder must itself state that it holds the required Ontario electrical contractor licence. A subcontractor's licence, to be confirmed later, does not satisfy the stated bidder-level requirement.
- Proposal D is flagged, not automatically rejected, because its completion schedule is unspecified and the 16-week requirement cannot be confirmed.
- Scores are calculated only for eligible, sufficiently assessable proposals. Flagged and ineligible proposals have a null rank and score.
- The scoring rubric and normalization formulas are design choices because the brief provides weights but does not prescribe formulas. These choices are documented in `WRITEUP.md` and should be approved by the administrator before operational use.


