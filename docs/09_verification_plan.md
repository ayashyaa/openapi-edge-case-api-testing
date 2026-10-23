# Verification and Evaluation Plan

**System Name:** OpenAPI Edge-Case API Testing Framework  
**Document Version:** 1.0  
**Scope:** Parsing, Contract Validation, Test Data Generation, Execution and Reporting Engines

---

## 1. Executive Summary & Evaluation Objectives

This document outlines the formal Verification and Evaluation Plan for the **OpenAPI Edge-Case API Testing Framework**. The objective is to establish measurable quality benchmarks, verification suites and empirical evaluation metrics for both primary functional streams of the project:

1. **Stream A (Module 1):** Specification Parsing & Contract Validation Engine.
2. **Stream B (Module 2):** Boundary Test Data Generator, HTTP Execution and Reporting Framework.

---

## 2. Part 1: Parsing and Contract Validation Module

### 2.1 Verification Plan & Test Suites

The parsing and contract validation engine is verified through a combination of unit, integration, and stress tests to ensure reliable processing of OpenAPI specifications and strict HTTP response schema assertion.

#### Verification Execution Strategy:
*   **Unit Verification:**
    *   Parsing valid and malformed YAML/JSON specifications.
    *   Dereferencing local (`#/components/schemas/`), remote (HTTP URL), and circular `$ref` JSON pointers.
    *   Extracting field-level constraints (`minimum`, `maximum`, `minLength`, `maxLength`, `pattern`, `enum`, `required`, data types).
*   **Integration Verification:**
    *   Ingesting large production specifications ($>10,000$ lines of YAML).
    *   Validating real HTTP response payloads against declared response JSON schemas.

### 2.2 Metrics & Mathematical Formulations

| Metric ID | Metric Name | Definition & Mathematical Formula | Target Benchmark |
|---|---|---|---|
| **M-1.1** | **Parsing Success Rate ($SR_{parse}$)** | Ratio of valid OpenAPI specifications successfully parsed without execution failure.<br>$$SR_{parse} = \frac{N_{parsed\_valid}}{N_{total\_valid}} \times 100\%$$ | **100%** on valid schemas |
| **M-1.2** | **Parsing Latency ($T_{parse}$)** | Wall-clock time required to parse a specification file and expand all `$ref` pointers. | $T_{parse} \le 1.0\text{ s}$ (specs $< 500\text{ KB}$) |
| **M-1.3** | **Reference Resolution Accuracy ($RA_{\$ref}$)** | Percentage of `$ref` JSON pointers correctly expanded without unresolved nodes or infinite loops. | **100%** accuracy |
| **M-1.4** | **Validation Precision ($P_{val}$)** | Percentage of response validation assertions correctly identifying schema compliance or violations. | **100%** precision |
| **M-1.5** | **Validation Overhead ($T_{val}$)** | Time required to validate a single HTTP response JSON body against its contract schema. | $T_{val} \le 50\text{ ms}$ per payload |

### 2.3 Baseline Comparison

The parsing and validation module is benchmarked against standard industry Python reference implementations:
*   **Parsing Baseline:** `openapi-spec-validator` combined with `prance` (for dereferencing).
*   **Validation Baseline:** `jsonschema` (Draft 2020-12 reference validator).

---

## 3. Part 2: Test Data Generator, Execution, and Reporting Module

### 3.1 Verification Plan & Test Targets

The generator and reporting module is evaluated on its ability to synthesize valid, boundary (edge-case), and invalid payloads, execute them against live APIs, enforce timeouts and produce actionable defect reports.

#### Evaluation Testbed (Target APIs):
1. **Swagger Petstore API (OpenAPI v3.0):** Used as a standard control benchmark to evaluate valid payload generation, baseline schema conformance and false positive minimization.
2. **Demo Seeded Buggy SUT (FastAPI/Node.js):** A purpose-built microservice containing 10 intentional vulnerabilities to evaluate defect detection rate ($D_{found}$) and defect classification accuracy.

#### Seeded Vulnerabilities in Test SUT:
1. **Unhandled Integer Overflow:** Integer parameter receiving $2^{63}$ triggers uncaught 500 server crash.
2. **Database String Truncation:** String payload exceeding `maxLength` triggers uncaught DB exception (500).
3. **NullPointer Exception:** Omitting a mandatory field in nested JSON causes server crash (500).
4. **Type Confusion Crash:** Passing string types into unvalidated numeric parameters (500).
5. **Unhandled Enum Case:** Unlisted enum string triggers unhandled switch-case exception (500).
6. **Array Index Out-of-Bounds:** Sending empty array `[]` when `minItems: 1` causes crash (500).
7. **Regex Engine Panic:** Special characters violating `pattern` cause unhandled regex parsing failure (500).
8. **False Positive Rejection:** Server returning `400 Bad Request` on fully valid input.
9. **Endpoint Hang/Timeout:** Payload triggering an unoptimized loop exceeding $5.0\text{ s}$.
10. **Schema Contract Discrepancy:** Endpoint returning `200 OK` but omitting mandatory keys in the response JSON.

### 3.2 Key Evaluation Metrics

| Metric ID | Metric Name | Definition & Mathematical Formula | Target Benchmark |
|---|---|---|---|
| **M-2.1** | **Defects Discovered ($D_{found}$)** | Total count of unique HTTP 500 crashes or unhandled exceptions identified on the target SUT. | Maximize ($10/10$ on Seeded SUT) |
| **M-2.2** | **Schema Constraint Coverage ($CC_{schema}$)** | Percentage of declared field constraints exercised with boundary or invalid test payloads.<br>$$CC_{schema} = \frac{C_{exercised}}{C_{total}} \times 100\%$$ | $CC_{schema} \ge 90\%$ |
| **M-2.3** | **False Positive Rate ($FPR$)** | Percentage of reported defects that are invalid (e.g. expected 4xx rejections misclassified as defects).<br>$$FPR = \frac{FP}{FP + TP} \times 100\%$$ | $FPR < 5\%$ |
| **M-2.4** | **Payload Generation Rate ($R_{gen}$)** | Number of synthesized test payloads generated per second. | $R_{gen} \ge 500\text{ payloads/s}$ |
| **M-2.5** | **Execution Throughput ($RPS$)** | Dispatched HTTP requests processed per second against local SUT. | $RPS \ge 20\text{ req/s}$ |
| **M-2.6** | **cURL Reproduction Rate ($RSR_{curl}$)** | Percentage of exported `cURL` commands that re-trigger the defect when executed in a terminal. | **100%** reproducibility |

---

## 4. Automated Execution & CI/CD Command Verification

To execute the verification suite automatically within CI/CD pipelines (e.g. GitHub Actions), the following standardized CLI commands are established:

```bash
# 1. Execute Unit & Integration Tests for Spec Parser & Validator
pytest tests/unit/test_parser.py tests/unit/test_validator.py --cov=src --cov-report=xml

# 2. Run Boundary Generation & Execution Suite against Seeded Buggy SUT
python -m src.cli run \
  --spec tests/fixtures/seeded_buggy_sut.yaml \
  --target-url http://localhost:8000 \
  --timeout 5.0 \
  --output-dir reports/ \
  --format json,html,junit

# 3. Verify Report Artifact Generation
test -f reports/report.json && test -f reports/report.html && test -f reports/junit.xml
```

---

## 5. Summary Acceptance Matrix

| System Component | Target Feature | Acceptance Condition | Verification Method |
|---|---|---|---|
| **Parser** | Multi-format Ingestion | Parses valid YAML/JSON specs ($SR_{parse} = 100\%$) | Automated Unit Test Suite |
| **Resolver** | `$ref` Expansion | Expands circular & deep `$ref` without infinite loops | Automated Integration Test |
| **Validator** | Schema Conformance | Identifies missing fields and invalid types | Mock Response Test Suite |
| **Generator** | Boundary Payload Synthesis | Generates $min, max, min-1, max+1$ for numeric fields | AST Payload Inspection |
| **Generator** | Single Fault Isolation | Mutates exactly 1 field per negative request | Payload Diff Analysis |
| **Executor** | Timeout Handling | Aborts requests hanging $> 5.0\text{ s}$ and logs `TIMEOUT` | Controlled Delay Server Test |
| **Reporter** | Defect Classification | Separates 500 crashes from 400 validation rejections | Seeded SUT Execution Run |
| **Reporter** | cURL Reproduction | `cURL` command re-triggers exact server response | Terminal Execution Verification |