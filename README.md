# AI Engineering: Evaluation & Observability

A three-system AI engineering capstone focused on reliable extraction, evaluation, decision-making, and resilience under imperfect inputs and failures.

## Project Overview

This repository contains three independent systems:

### 1. Insurance Policy Pipeline
Extracts structured policy information, validates outputs, compares independent reviewer results, and routes decisions for human review, spot checks, or escalation.

### 2. Mortgage Document Extraction
Classifies mortgage-related documents, extracts structured fields, and checks consistency between line items and stated totals. Missing information is represented explicitly rather than fabricated.

### 3. Supply-Chain Risk Investigation
Combines evidence from multiple sources into an investigation briefing. It distinguishes well-established findings from contested or incomplete information and records source failures.

## Repository Structure

- `insurance-policy/` — policy extraction, validation, review, routing, fixtures, and tests.
- `mortgage-extraction/` — document extraction, schemas, consistency validation, fixtures, and tests.
- `supply-chain/` — source readers, investigation coordination, synthesis, fixtures, and tests.
- `evidence/` — test results, static-analysis reports, run outputs, calibration results, routing decisions, and screenshots.
- `perturbation-log.md` — deliberate input/configuration changes and observed behavior.
- `reflection-brief.md` — analysis of results, limitations, and reliability lessons.
- `environment.txt` — Python version and operating-system details.

## Reproducibility and Evidence

The `evidence/` directory contains artifacts organized by system, including test reports, `mypy` and `ruff` results, representative runs, and screenshots.

Start with:
- `evidence/01-policy-pipeline/`
- `evidence/02-mortgage-extraction/`
- `evidence/03-supply-chain/`

See `perturbation-log.md` for stress-test comparisons and `reflection-brief.md` for the analysis of findings across all three systems.

## Environment

The recorded environment is documented in `environment.txt`. Each system has its own `pyproject.toml` and README with system-specific details.

Some live API operations may require credentials and access to a compatible model provider. Never commit real API keys or secrets; use the supplied example environment files where applicable.

## Scope

This repository emphasizes evaluation and observability: validating extracted data, surfacing disagreements, tracking uncertainty, preserving missing information, and making failures visible rather than silently treating them as successful results.
