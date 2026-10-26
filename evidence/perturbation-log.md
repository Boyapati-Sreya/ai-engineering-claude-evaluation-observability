# Perturbation Log

## System 1 — Validated, Routed Insurance Policy Extraction Pipeline

- Change: A policy input with the `endorsements` source missing was used to exercise the missing-source guard.
- Evidence: `policy-tests.txt` / focused test `test_ac_01_04_missing_source_halts_immediately`.
- Observed behavior: the pipeline escalated with `RetryFutileEscalation`, detected pattern `endorsements_absent`, and made exactly one API call.
- Additional routing evidence: `routing_decisions.json` contains the generated routing decisions; `calibration-output.txt` contains the sliced calibration results.

## System 2 — Resilient Mortgage Document Extraction System

- Change: The stated monthly income was deliberately changed from a value within the validator tolerance to a value beyond the $1 tolerance.
- Unperturbed behavior: `test_ac_04_02_consistent_when_within_tolerance` passed with the extraction considered consistent.
- Perturbed behavior: `test_ac_04_02_inconsistent_when_beyond_tolerance` passed with the extraction considered inconsistent and a discrepancy recorded.
- Evidence: `mortgage-perturbation.txt` — both contrast tests passed.

## System 3 — Resilient Supply-Chain Coordinator

- Change: The logistics source was deliberately made unavailable through the timeout test scenario.
- Normal behavior: `supply-briefing.txt` contains the normal multi-source briefing with Well-Established, Contested, and Incomplete claims.
- Perturbed behavior: `supply-timeout.txt` records `logistics unavailable (timeout)` and annotates affected claims as incomplete while allowing successful sources to continue.
- Evidence: `supply-perturbation.txt` — `test_logistics_timeout_returns_partial_and_context`, `test_coordinator_proceeds_and_annotates_gap`, and related coordinator resilience tests; 6 passed.
