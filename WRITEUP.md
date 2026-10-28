# Design Write-up

## Sample outcome and assumptions

The expected output is in [ranking.json](ranking.json). Proposal B is ineligible because the required licence must be held by the bidder; a subcontractor to be selected after award does not satisfy that mandatory condition. Proposal C is quarantined for human review because it contains an instruction aimed at the automated reviewer. The instruction is never treated as evidence. If a reviewer confirms the document is safe to evaluate, C would score 76 and place behind D.

For this demonstration, the admin-configured score bands are:

| Criterion | Deterministic scoring rule |
|---|---|
| Relevant experience (30) | 10+ qualifying projects: 30; 7–9: 24; 4–6: 18; 1–3: 9; none stated: 0 |
| Price (25) | At or below 90% of budget: 25; above 90% through 95%: 20; above 95% through 100%: 15; above budget: 0 and review |
| Timeline (15) | Up to 12 weeks: 15; 13–14: 10; 15–16: 5; longer or unstated: 0 |
| Warranty (15) | Panel warranty of at least 25 years: 5; workmanship of 12+ years: 10, 10–11 years: 8, 5–9 years: 4, otherwise: 0 |
| Team and approach (15) | 3 points each for a named/committed lead, crew detail, safety plan, monitoring plan, and delivery detail |

These bands are illustrative category configuration, not application constants. They produce A 93, E 84, D 77 and, if cleared, C 76. Equal totals are resolved by relevant experience, then price, then upload ID so the order is stable.

## 1. AI vs. rules

AI extracts structured facts from varied proposal language and returns a short source citation, location and confidence for every field. It also identifies text that looks like instructions, hidden content, or contradictory evidence. OCR, file validation, schema validation, mandatory checks, score calculation, tie-breaking, ranking and status assignment are plain code.

The boundary is intentional: interpreting unstructured language benefits from AI, while arithmetic and policy enforcement do not. The model never receives authority to change criteria, infer missing facts, decide eligibility, assign points or select a winner.

## 2. Consistency and explainability

Every run pins the document hash, extracted text, model and prompt versions, criteria version, and scoring-engine version. The extractor uses temperature zero and a strict JSON schema. Code rejects uncited values, invalid types and evidence not present in the extracted text; uncertain or conflicting fields go to review.

With the same pinned inputs and confirmed evidence, eligibility, scores and order are identical. A reviewer sees a criterion-by-criterion scorecard containing the configured rule, extracted value, points, source excerpt and page, plus the mandatory-gate result and tie-break used. AI output is evidence for review, not the explanation by itself.

## 3. Human in the loop

A reviewer previews extraction, resolves low-confidence, missing, contradictory or suspicious fields, and confirms the evaluation batch before ranking. They may correct extracted facts with a reason and source, or override a status/ranking only with a required justification. A separate authorized person makes the award; the tool only recommends an order.

The audit log is append-only and records actor, timestamp, proposal hash, criteria/prompt/model/engine versions, extracted evidence, validation results, scores, flags, reviewer edits, overrides, before/after values and the final decision. Original files and prior results remain retained according to policy.

## 4. Changing criteria

Publishing an edit creates a new immutable criteria version; it never silently rewrites an old result. Existing batches remain linked to their original version and display “outdated criteria” when a newer one exists. An authorized reviewer can start an explicit re-evaluation of the whole comparison set against the new version. The new run is stored beside the old run with a change summary, preserving reproducibility and preventing mixed-version rankings.

## 5. Risks and safeguards

- **Bad OCR or extraction:** validate file type and page count, retain page coordinates, require citations, use confidence thresholds, and show the source beside each field.
- **Hallucinated or omitted facts:** forbid inference, verify quoted evidence against extracted text, treat absence as unknown, and route material uncertainty to a person.
- **Prompt injection or document manipulation:** isolate proposal text as untrusted data, instruct the model to ignore embedded commands, scan hidden layers/metadata, flag suspicious text, and never let model output execute actions. This is why C is flagged.
- **Bias:** score only published job-related criteria, avoid bidder identity where practical, test outcomes across comparable cases, and periodically review criteria and overrides for disparate patterns.
- **Inconsistent comparisons:** freeze the full batch and configuration version before scoring; re-score all proposals together after an approved change.
- **Security and privacy:** malware-scan uploads, encrypt data, apply least-privilege access and retention controls, and never send proposal content to an unapproved model provider.
- **Cost or outages:** cap pages and retries, cache extraction by document hash, monitor usage, queue work, and allow manual evidence entry when AI is unavailable.
