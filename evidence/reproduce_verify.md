# Reproduce and Verify

## Environment
- Python version: Python 3.13.0
- Operating system: Linux 6.6.97+ x86_64

## Policy pipeline
- Test command: `pytest tests/ -v`
- Result: 48 tests collected; 45 passed, 3 skipped. The 3 skipped tests are live API tests because `claude-haiku-4-5` was unavailable to the Vocareum organization.
- mypy result: All policy source files passed mypy with no issues.
- ruff result: All policy source files passed Ruff with no issues.

## Mortgage extractor
- Test command: `pytest tests/ -v`
- Result: 25 passed in 1.16s.
- mypy result: `Success: no issues found in 11 source files`
- ruff result: All checks passed.

## Supply-chain investigator
- Test command: `pytest tests/ -v`
- Result: 34 passed in 33.20s.
- mypy result: `Success: no issues found in 8 source files`
- ruff result: All checks passed.

## Evidence files
- Policy routing decisions: `routing_decisions.json`
- Policy calibration: `calibration-output.txt`
- Mortgage extraction: `mortgage-appraisal.txt`
- Mortgage mismatch: `mortgage-mismatch.txt`
- Mortgage missing-field behavior: `mortgage-missing-bonus.txt`
- Supply-chain briefing: `supply-briefing.txt`
- Supply-chain timeout behavior: `supply-timeout.txt`
- Reflection: `reflection-brief.md`
- Perturbation evidence: `perturbation-log.md`


## Full Verification Outputs

### Policy pipeline

#### policy-tests.txt
```text
============================= test session starts ==============================
platform linux -- Python 3.13.0, pytest-9.1.1, pluggy-1.6.0 -- /workspace/Build a Validated, Routed Insurance Policy Extraction Pipeline/04-hitl-routing/solution/.venv/bin/python3.13
cachedir: .pytest_cache
rootdir: /workspace/Build a Validated, Routed Insurance Policy Extraction Pipeline/04-hitl-routing/solution
configfile: pyproject.toml
plugins: asyncio-1.4.0, anyio-4.15.1
asyncio: mode=Mode.AUTO, debug=False, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function
collecting ... collected 48 items

tests/test_us01_retry.py::test_ac_01_01_extract_tool_has_required_fields PASSED [  2%]
tests/test_us01_retry.py::test_ac_01_01_policy_type_uses_resilient_catchall_enum PASSED [  4%]
tests/test_us01_retry.py::test_ac_01_01_system_prompt_instructs_null_for_unstated_fields PASSED [  6%]
tests/test_us01_retry.py::test_ac_01_01_extractor_parses_well_formed_response PASSED [  8%]
tests/test_us01_retry.py::test_ac_01_02_validator_flags_negative_premium_as_format PASSED [ 10%]
tests/test_us01_retry.py::test_ac_01_02_validator_flags_null_required_as_missing_source PASSED [ 12%]
tests/test_us01_retry.py::test_ac_01_02_validator_flags_consistency_premium_mismatch PASSED [ 14%]
tests/test_us01_retry.py::test_ac_01_02_validator_accepts_well_formed PASSED [ 16%]
tests/test_us01_retry.py::test_ac_01_03_format_failure_retries_with_error_appended PASSED [ 18%]
tests/test_us01_retry.py::test_ac_01_03_consistency_failure_also_retries PASSED [ 20%]
tests/test_us01_retry.py::test_ac_01_04_missing_source_halts_immediately PASSED [ 22%]
tests/test_us01_retry.py::test_ac_01_05_success_metadata_after_retry PASSED [ 25%]
tests/test_us01_retry.py::test_ac_01_06_pattern_summary_counts_across_runs PASSED [ 27%]
tests/test_us01_retry.py::test_live_extracts_well_formed_policy SKIPPED  [ 29%]
tests/test_us02_batch.py::test_ac_02_04_submission_frequency_meets_sla PASSED [ 31%]
tests/test_us02_batch.py::test_ac_02_04_submission_frequency_raises_when_sla_below_batch_eta PASSED [ 33%]
tests/test_us02_batch.py::test_ac_02_04_submission_frequency_clamps_to_minimum_one_per_day PASSED [ 35%]
tests/test_us02_batch.py::test_ac_02_01_submitter_attaches_custom_id_per_policy PASSED [ 37%]
tests/test_us02_batch.py::test_ac_02_02_poller_returns_results_keyed_by_custom_id PASSED [ 39%]
tests/test_us02_batch.py::test_ac_02_03_errored_items_resubmitted_in_one_followup_batch PASSED [ 41%]
tests/test_us02_batch.py::test_ac_02_03_persistent_errors_after_resubmission_are_returned_as_escalation PASSED [ 43%]
tests/test_us02_batch.py::test_ac_02_05_format_failure_triggers_resubmission_with_error_feedback PASSED [ 45%]
tests/test_us02_batch.py::test_ac_02_05_missing_source_escalates_immediately_no_resubmission PASSED [ 47%]
tests/test_us02_batch.py::test_ac_02_06_dry_run_sample_returns_success_rate_and_pattern_summary PASSED [ 50%]
tests/test_us02_batch.py::test_ac_02_06_dry_run_sample_caps_at_input_size PASSED [ 52%]
tests/test_us02_batch.py::test_live_dry_run_sample_against_real_api SKIPPED [ 54%]
tests/test_us03_review.py::test_ac_03_01_reviewer_prompt_is_a_fresh_message_list PASSED [ 56%]
tests/test_us03_review.py::test_ac_03_04_reviewer_prompt_has_no_extractor_artefacts PASSED [ 58%]
tests/test_us03_review.py::test_ac_03_02_reviewer_returns_per_field_agreement_with_review_confidence PASSED [ 60%]
tests/test_us03_review.py::test_ac_03_03_any_disagreement_flags_review_disagreement PASSED [ 62%]
tests/test_us03_review.py::test_ac_03_05_extractor_and_reviewer_use_different_models PASSED [ 64%]
tests/test_us03_review.py::test_ac_03_05_independent_review_uses_reviewer_model_by_default PASSED [ 66%]
tests/test_us03_review.py::test_ac_03_06_coverage_limit_must_exceed_endorsement_limits_sum PASSED [ 68%]
tests/test_us03_review.py::test_ac_03_06_coverage_limit_check_passes_when_consistent PASSED [ 70%]
tests/test_us03_review.py::test_ac_03_06_endorsement_exclusion_contradiction_detected PASSED [ 72%]
tests/test_us03_review.py::test_ac_03_06_endorsement_exclusion_no_contradiction_passes PASSED [ 75%]
tests/test_us03_review.py::test_ac_03_06_premium_vs_components_consistency PASSED [ 77%]
tests/test_us03_review.py::test_ac_03_06_premium_components_check_skipped_when_components_absent PASSED [ 79%]
tests/test_us03_review.py::test_live_reviewer_returns_per_field_judgement SKIPPED [ 81%]
tests/test_us04_routing.py::test_ac_04_02_all_clear_routes_to_auto_approve PASSED [ 83%]
tests/test_us04_routing.py::test_ac_04_02_low_confidence_routes_to_human_review PASSED [ 85%]
tests/test_us04_routing.py::test_ac_04_02_integration_failure_routes_to_human_review PASSED [ 87%]
tests/test_us04_routing.py::test_ac_04_06_high_confidence_plus_reviewer_disagreement_still_routes_to_human_review PASSED [ 89%]
tests/test_us04_routing.py::test_ac_04_03_stratified_sampler_picks_from_every_stratum_with_eligible_records PASSED [ 91%]
tests/test_us04_routing.py::test_ac_04_03_only_auto_approve_decisions_are_eligible_for_spot_check PASSED [ 93%]
tests/test_us04_routing.py::test_ac_04_04_calibration_report_is_sliced_by_policy_type_and_field PASSED [ 95%]
tests/test_us04_routing.py::test_ac_04_04_calibration_report_handles_empty_input PASSED [ 97%]
tests/test_us04_routing.py::test_ac_04_05_routing_decisions_written_to_json_file PASSED [100%]

======================== 45 passed, 3 skipped in 0.18s =========================

```

#### policy-mypy.txt
```text
Success: no issues found in 17 source files

```

#### policy-ruff.txt
```text
All checks passed!

```

### Mortgage extractor

#### mortgage-tests.txt
```text
============================= test session starts ==============================
platform linux -- Python 3.13.0, pytest-9.1.1, pluggy-1.6.0 -- /workspace/Build a Resilient Mortgage Document Extraction System/04-validate-mathematical-consistency/solution/.venv/bin/python3.13
cachedir: .pytest_cache
rootdir: /workspace/Build a Resilient Mortgage Document Extraction System/04-validate-mathematical-consistency/solution
configfile: pyproject.toml
plugins: anyio-4.15.1
collecting ... collected 25 items

tests/test_us01_schema.py::test_ac_01_01_tool_definition_shape PASSED    [  4%]
tests/test_us01_schema.py::test_ac_01_02_required_and_optional_fields PASSED [  8%]
tests/test_us01_schema.py::test_ac_01_03_enum_other_with_detail_field PASSED [ 12%]
tests/test_us01_schema.py::test_ac_01_04_at_least_three_nullable_fields PASSED [ 16%]
tests/test_us01_schema.py::test_ac_01_05_list_nullable_fields_returns_dotted_paths PASSED [ 20%]
tests/test_us02_pipeline.py::test_ac_02_01_classify_uses_forced_tool_choice PASSED [ 24%]
tests/test_us02_pipeline.py::test_ac_02_02_extract_uses_any_tool_choice_against_doc_type_tools PASSED [ 28%]
tests/test_us02_pipeline.py::test_ac_02_03_extract_returns_typed_mortgage_extraction PASSED [ 32%]
tests/test_us02_pipeline.py::test_ac_02_04_other_short_circuits_before_extract PASSED [ 36%]
tests/test_us02_pipeline.py::test_ac_02_04_extract_directly_with_other_raises PASSED [ 40%]
tests/test_us02_pipeline.py::test_ac_02_05_default_model_is_haiku_and_overridable PASSED [ 44%]
tests/test_us03_prompts.py::test_ac_03_01_system_prompt_has_numbered_criteria_with_verbatim_first PASSED [ 48%]
tests/test_us03_prompts.py::test_ac_03_02_four_contrastive_examples PASSED [ 52%]
tests/test_us03_prompts.py::test_ac_03_02b_each_example_has_inline_reasoning PASSED [ 56%]
tests/test_us03_prompts.py::test_ac_03_03_missing_bonus_returns_none PASSED [ 60%]
tests/test_us03_prompts.py::test_ac_03_04_informal_sqft_normalized_to_integer PASSED [ 64%]
tests/test_us03_prompts.py::test_ac_03_05_normalization_rules_is_single_referenced_constant PASSED [ 68%]
tests/test_us04_validator.py::test_ac_04_01_extraction_carries_both_calculated_and_stated PASSED [ 72%]
tests/test_us04_validator.py::test_ac_04_02_consistent_when_within_tolerance PASSED [ 76%]
tests/test_us04_validator.py::test_ac_04_02_inconsistent_when_beyond_tolerance PASSED [ 80%]
tests/test_us04_validator.py::test_ac_04_02_default_tolerance_is_one_dollar PASSED [ 84%]
tests/test_us04_validator.py::test_ac_04_03_discrepancy_carries_field_calc_stated_delta PASSED [ 88%]
tests/test_us04_validator.py::test_ac_04_04_real_paystub_with_sum_mismatch_is_flagged PASSED [ 92%]
tests/test_us04_validator.py::test_clean_extraction_yields_consistent_true PASSED [ 96%]
tests/test_us04_validator.py::test_validation_report_round_trips_through_json PASSED [100%]

============================== 25 passed in 1.16s ==============================

```

#### mortgage-mypy.txt
```text
Success: no issues found in 11 source files

```

#### mortgage-ruff.txt
```text
All checks passed!

```

### Supply-chain investigator

#### supply-tests.txt
```text
============================= test session starts ==============================
platform linux -- Python 3.13.0, pytest-9.1.1, pluggy-1.6.0 -- /workspace/Investigate Supply Chain Risk with Multi-Source Synthesis/03-resilient-coordinator/solution/.venv/bin/python3.13
cachedir: .pytest_cache
rootdir: /workspace/Investigate Supply Chain Risk with Multi-Source Synthesis/03-resilient-coordinator/solution
configfile: pyproject.toml
plugins: anyio-4.15.1
collecting ... collected 34 items

tests/test_coordinator.py::test_logistics_timeout_returns_partial_and_context PASSED [  2%]
tests/test_coordinator.py::test_coordinator_proceeds_and_annotates_gap PASSED [  5%]
tests/test_coordinator.py::test_access_failure_distinct_from_empty_result PASSED [  8%]
tests/test_coordinator.py::test_single_failure_does_not_abort PASSED     [ 11%]
tests/test_coordinator.py::test_only_successful_claims_vectorized PASSED [ 14%]
tests/test_coordinator.py::test_healthy_run_is_contested PASSED          [ 17%]
tests/test_memory.py::test_shared_memory_constructs_offline PASSED       [ 20%]
tests/test_memory.py::test_provenance_round_trips PASSED                 [ 23%]
tests/test_memory.py::test_search_retrieves_cross_source_corroborating_claims PASSED [ 26%]
tests/test_memory.py::test_related_to_excludes_self_and_finds_cross_source PASSED [ 29%]
tests/test_memory.py::test_deterministic_results PASSED                  [ 32%]
tests/test_readers.py::test_claim_has_required_fields PASSED             [ 35%]
tests/test_readers.py::test_claim_requires_source_date PASSED            [ 38%]
tests/test_readers.py::test_claim_rejects_out_of_range_confidence PASSED [ 41%]
tests/test_readers.py::test_claim_rejects_unparseable_date PASSED        [ 44%]
tests/test_readers.py::test_read_audit PASSED                            [ 47%]
tests/test_readers.py::test_read_logistics PASSED                        [ 50%]
tests/test_readers.py::test_read_news_extracts_dated_claims PASSED       [ 52%]
tests/test_readers.py::test_anthropic_extractor_uses_structured_output PASSED [ 55%]
tests/test_readers.py::test_empty_result_is_ok_not_error PASSED          [ 58%]
tests/test_readers.py::test_missing_file_is_access_failure PASSED        [ 61%]
tests/test_readers.py::test_ambiguous_match_sets_needs_identifier PASSED [ 64%]
tests/test_readers.py::test_read_quality PASSED                          [ 67%]
tests/test_readers.py::test_all_readers_emit_provenance PASSED           [ 70%]
tests/test_synthesis.py::test_group_merges_near_duplicate_metrics PASSED [ 73%]
tests/test_synthesis.py::test_group_keeps_distinct_metrics_separate PASSED [ 76%]
tests/test_synthesis.py::test_classification PASSED                      [ 79%]
tests/test_synthesis.py::test_contested_preserves_both_values PASSED     [ 82%]
tests/test_synthesis.py::test_content_appropriate_rendering PASSED       [ 85%]
tests/test_synthesis.py::test_escalation_criteria PASSED                 [ 88%]
tests/test_synthesis.py::test_escalation_ignores_confidence_directly PASSED [ 91%]
tests/test_synthesis.py::test_three_sections_with_attribution PASSED     [ 94%]
tests/test_synthesis.py::test_empty_section_renders_none PASSED          [ 97%]
tests/test_synthesis.py::test_coverage_badges PASSED                     [100%]

============================= 34 passed in 33.20s ==============================

```

#### supply-mypy.txt
```text
Success: no issues found in 8 source files

```

#### supply-ruff.txt
```text
All checks passed!

```
