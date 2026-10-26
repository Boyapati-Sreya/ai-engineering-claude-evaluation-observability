# Reflection Brief — Evaluation and Observability Capstone

**Name:*Sreya Boyapati*
**Date:*September 25,2026*

> Ground every answer in your own run. When a question asks for a number, file name, or line, paste
> it from your artifacts — a reviewer should be able to find it. Answers that are correct in the
> abstract but cite nothing do not meet the bar. Keep it short and specific.

---

## 0. Environment

| Field | Value |
|---|---|
| OS & version | Linux 6.6.97+ x86_64 |
| Python version | Python 3.13.0 |
| Date run | Thu Sep 24 20:35:20 UTC 2026 |
| Ran any system live? (which) | Yes — System 1 (Validated, routed insurance policy pipeline) |

---

## 1. Validated, routed pipeline

| Evidence | Value |
|---|---|
| Passing test count | 45 passed, 3 skipped — the three skipped tests were live API tests in the earlier test run because `claude-haiku-4-5` was unavailable to the Vocareum organization; a later live pipeline run succeeded using `claude-haiku-4-5-20251001` |
| Routing output file | `routing_decisions.json` |
| auto_approve / human_review / spot_check counts | 0 / 8 / 1 |
| Total routing decisions / escalations | 9 decisions / 1 escalation — `routing_decisions.json` |

**1a. Retry boundary.** From your perturbation run (a required field removed), paste the escalation
record. How many API calls did the system make, and why is retrying a futile case worse than
escalating it?

> The perturbation test `test_ac_01_04_missing_source_halts_immediately` produced a `RetryFutileEscalation` for the missing `endorsements` field, with `detected_pattern="endorsements_absent"`. The test verifies `client.call_count == 1`, so the system made exactly one API call. Retrying is futile because the required information is absent from the source; another model call cannot recover evidence that is not present, while escalation makes the missing evidence explicit for human handling.

**1b. Reading the router.** Pick one `human_review` record from your routing output. Which of the
three signals (confidence, reviewer, integration) sent it to a human? If you had trusted the model's
confidence alone, what would have happened?

> `POL-2025-002` was routed to `human_review` because of the **reviewer** signal: the independent reviewer disagreed on `coverage_limit` and `deductible`. The record had no `fields_below_threshold` and no integration failure, so confidence alone would not have triggered human review. Relying only on model confidence would therefore have allowed this record to avoid the human-review path.

**1c. Where the aggregate lies.** Run the calibration snippet. Quote the one cell whose accuracy lags
its confidence, plus the overall figure. What does slicing by `policy_type × field` catch that a
single number hides?

> The `umbrella × exclusions` slice reports `n=2 conf=0.93 acc=0.00 brier=0.865`, while the overall calibration result is `OVERALL brier=0.291`. The slice exposes a specific case where confidence is high but accuracy is zero, which an aggregate figure can hide by averaging behavior across different policy types and fields.

## 2. Schema-enforced two-pass extraction

| Evidence | Value |
|---|---|
| Passing test count | 25 passed — `mortgage-tests.txt` |
| Document run | `mortgage-appraisal.txt`, `mortgage-missing-bonus.txt`, `mortgage-mismatch.txt` |
| Classified type | `appraisal` — `mortgage-classification.txt` |

**2a. Two guarantees.** Paste your discrepancy-run output. Tool use already forces valid JSON, yet the
validator still catches a bad sum. Why are these two different guarantees? Name one error each
cannot catch.

> `mortgage-mismatch.txt` reports `"consistent": false` and a `total_monthly_income` discrepancy: calculated `9642.17`, stated `10892.17`, delta `-1250.0`. Tool use guarantees that the extraction conforms to the expected structured JSON/tool schema, while the validator checks a separate domain invariant: whether the income components mathematically agree with the stated total. Valid JSON could still contain an incorrect sum; mathematical validation cannot catch a malformed or schema-invalid response.

**2b. Refusing to fabricate.** Run on a document missing a field. Paste that field's output. Why null
instead of an invented value? Point to the schema choice that allows it.

> `mortgage-missing-bonus.txt` shows `"bonus_monthly": null` and `"bonus_ytd": null`. The extraction leaves the values null because the source does not provide them, rather than inventing numbers. This is enabled by the schema's nullable/optional field design.

**2c. Normalization.** Quote one field where the source text and extracted value differ in format
("about 2,400 sq ft" → `2400`). Why normalize at extraction time rather than downstream?

> `mortgage-appraisal.txt` shows `gross_living_area_sqft: 2400`, corresponding to the source's informal `"about 2,400 sq ft"` representation. Normalizing during extraction produces a typed numeric value that downstream validation and comparison can use directly instead of repeatedly parsing different textual formats later.


**2d. Perturbation evidence.** `mortgage-perturbation.txt` records the deliberate tolerance change: the within-tolerance case remained consistent, while the beyond-$1 case became inconsistent. Both contrast tests passed.

## 3. Multi-source synthesis

| Evidence | Value |
|---|---|
| Passing test count | 34 passed — `supply-tests.txt` |
| Briefing file | `supply-briefing.txt`|
| Section the conflict landed in | Contested — `on_time_delivery_rate` |

**3a. Annotate, don't arbitrate.** Quote one conflicting-metric pair from your briefing — both values,
sources, dates. Give one way a reader is better served by the preserved conflict than by a single
reconciled number.

> `on_time_delivery_rate`: **95.0%** — supplier_audit, as of **2026-04-10**; **78.0%** — logistics, as of **2026-04-05**. Preserving both values with their sources and dates lets the reader see the disagreement and evaluate the evidence instead of being given one unsupported reconciled number. Artifact: `supply-briefing.txt`.

**3b. Source goes dark.** Run with `--simulate-timeout`. Paste the part of the briefing showing the
failed source. How is "unreachable" handled differently from "nothing to report," and why does the run
still finish?

> `Sources unavailable: logistics unavailable (timeout)`. The affected `late_shipment_count` is placed under **Incomplete** with `missing source: timeout reading logistics`. This distinguishes an unreachable source from a legitimate empty result: the system records that the data could not be retrieved rather than treating it as evidence that there was nothing to report. The run still finishes because the investigation continues with the sources that were successfully retrieved. Artifact: `supply-timeout.txt`.


**3c. Dates as a guardrail.** Quote two claims about the same supplier with different dates. How does
requiring a date stop a time difference from reading as a contradiction?

> `on_time_delivery_rate`: **95.0%** from `supplier_audit`, as of **2026-04-10**, versus **78.0%** from `logistics`, as of **2026-04-05**. Requiring dates prevents the two values from being interpreted as measurements taken at the same time. The different dates make it clear that a change over time could contribute to the difference, so the system preserves the disagreement instead of automatically treating it as a direct contradiction. Artifact: `supply-briefing.txt`.


**3d. Perturbation evidence.** `supply-perturbation.txt` records the deliberate logistics timeout. The perturbed run preserved successful-source results, annotated the failed logistics source as incomplete, and completed the coordinator run; 6 resilience tests passed.

## 4. Synthesis

**4a. One principle.** Name the single moment in your runs (system + artifact) where *evaluate the
output, don't trust the model's word* most clearly caught something a trusting design would have
shipped.

> In **System 2 — Mortgage Document Extraction**, the mismatch fixture showed why the extracted values must be evaluated rather than trusted. `mortgage-mismatch.txt` reported `validation consistent false` and identified a **$1,250 discrepancy** between the calculated and stated `total_monthly_income`. A trusting design could have accepted the extracted numbers as valid, while the mathematical consistency check caught the contradiction before it could be treated as a valid result.


**4b. Confidence ≠ correctness.** Pick the system where this mattered most, and explain why using
something you observed.

> This mattered most in **System 1 — the validated, routed insurance policy extraction pipeline**. The calibration output showed the `umbrella × exclusions` slice with **confidence 0.93 but accuracy 0.00**, with a **Brier score of 0.865**. This directly showed that a high confidence value did not guarantee a correct result. Artifact: `calibration-output.txt`.


**4c. Apply it.** Describe a real workflow where an LLM pulls structured results from messy input.
Which pattern — validated retry with escalation, independent review with deterministic routing, or
provenance-preserving conflict annotation — would you reach for first, and what would you instrument
to know when it broke?

> In the insurance-policy extraction pipeline, an LLM pulls structured fields from unstructured policy documents. I would reach for **independent review with deterministic routing** first, because the extraction can be evaluated against validation and reviewer signals rather than relying only on the model's confidence. I would instrument extraction confidence, validation failures, reviewer disagreements, human-review and escalation rates, retry counts, and the final routing decision. I would also track these metrics by field and document type so that a high-confidence but inaccurate slice could be detected rather than hidden by an aggregate metric. This is supported by the System 1 routing and calibration artifacts: `routing_decisions.json` and `calibration-output.txt`.
