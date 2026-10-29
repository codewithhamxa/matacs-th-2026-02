# Design Write-up — AI Proposal Triage

## 1. AI vs. rules

Use AI for document understanding: extract the required facts from each proposal, normalize them into a fixed schema, and return short evidence snippets plus page references where available. AI is useful because proposals may describe the same concept in different wording or place information in different sections.

Use ordinary application code for mandatory pass/fail checks, numeric scoring, weight application, sorting, and validation. These steps are explicit business rules and should be deterministic, testable, and auditable. The AI must not choose its own criteria, invent missing facts, or assign the final score. A schema validator rejects malformed output; missing or ambiguous evidence is marked `unknown` and sent for review.

## 2. Consistency and explainability

The admin configuration is versioned and includes criterion IDs, types, weights, thresholds, scoring formulas, and effective dates. For the same extracted facts and configuration version, deterministic code produces the same score and ranking. Store the source document hash, extraction model/prompt version, structured extraction, evidence, validation outcomes, criterion-level scores, formula version, and final output.

The reviewer sees each proposal's eligibility status, score breakdown, evidence supporting each criterion, missing information, and the reason for its position. Use stable tie-break rules (higher experience score, then lower total price, then alphabetical proposal letter) so ties are reproducible. The final award remains a human decision.

## 3. Human in the loop and audit

A reviewer confirms extracted facts and evidence, resolves mandatory failures or unknowns, and reviews any suspicious instruction found inside a document. A reviewer may correct an extraction or override a result, but must enter a reason. Record the original and revised values, user ID, timestamp, document version/hash, configuration version, model/prompt version, score before and after, and override rationale. Require an explicit human approval before any award recommendation is finalized. Do not automatically award a contract.

## 4. Changing criteria

Every admin edit creates a new immutable configuration version; it does not silently change historical results. Each assessment run references the version used. If weights or criteria change, show the admin which proposals are affected and offer an explicit re-score of the complete comparison set using the new version. Preserve the previous ranking for audit and display before/after differences. Validate that weighted criteria sum to 100%, mandatory criteria have no weight, and all thresholds/formulas are valid before publishing a configuration.

## 5. Risks and safeguards

- **Incorrect extraction or OCR:** retain evidence and page references; validate data types and ranges; flag low-confidence, conflicting, or missing values for human review.
- **Hallucinated facts:** instruct the model to return `null` when evidence is absent; never accept unsupported claims; verify every extracted value against the source.
- **Prompt injection in proposal text:** treat the whole document as untrusted data. Ignore document instructions that attempt to change ranking, criteria, or system behavior. Proposal C contains such an instruction; it must not affect scoring and should be flagged for reviewer awareness.
- **Bias or inconsistent judgment:** use published, category-specific criteria and fixed formulas; test with representative examples; avoid inferring protected or irrelevant characteristics.
- **Manipulated or misleading claims:** preserve source evidence, highlight conflicts, and require human confirmation of mandatory credentials. Where external verification is needed, record it as a separate verified check rather than pretending the PDF proves it.
- **Missing timeline or other material facts:** do not silently treat missing information as zero or as a pass. Mark the proposal `flagged`, with null rank/score until the reviewer resolves it.
- **API cost, outages, and data exposure:** limit file size and token usage, cache extraction by document hash, retry safely, use timeouts and rate limits, monitor cost, and avoid sending real confidential data to an unapproved model provider. Use access controls, encryption, and retention limits.
- **Reproducibility and accidental changes:** store model/prompt/configuration versions and deterministic scoring outputs; validate JSON against a schema before displaying results.

## Scoring assumptions used for the sample

The brief specifies weights but not formulas, so this design uses a transparent illustrative rubric that an admin would approve before production use:

1. **Experience (30 points):** 0 qualifying projects = 0; 1–3 = 40; 4–6 = 60; 7–9 = 80; 10 or more = 100. Multiply the criterion score by 30%.
2. **Price (25 points):** among eligible, assessable proposals, score = lowest eligible price ÷ proposal price × 100, capped at 100. Multiply by 25%.
3. **Timeline (15 points):** for a confirmed timeline within the 16-week limit, score = (16 − completion weeks) ÷ 16 × 100, bounded between 0 and 100. This is a simple linear normalization, not a requirement specified by the brief.
4. **Warranty (15 points):** panel warranty contributes 50% of this criterion, normalized against 25 years; workmanship warranty contributes 50%, normalized against 12 years. Each component is capped at 100.
5. **Team and approach (15 points):** 25 points each for a named project lead, stated crew, safety plan, and monitoring plan. Other useful details are shown to the reviewer but do not add points in this simple rubric.

The total score is the sum of each 0–100 criterion score multiplied by its configured weight. Scores are rounded to two decimal places only for display; calculations use unrounded values. Proposal B is ineligible because the proposal does not state that the bidder itself holds the required licence. Proposal D is flagged because its timeline is unspecified, so its eligibility against the 16-week requirement and weighted timeline score cannot be confirmed. Proposal C's embedded instruction is treated as untrusted content, not an evaluation rule.

## Sample ranking outcome

Using the assumptions above, the eligible proposals rank A (87.50), E (81.35), and C (71.51). B is ineligible and D is flagged. These numbers are illustrative outputs of the stated rubric, not scores prescribed by MATACS. The reviewer must confirm evidence and resolve flags before any award decision.
