# Requirements Specification: Test Data Generation, Execution, and Reporting Engine

**System Name:** OpenAPI Edge-Case API Testing Framework

**Module:** Test Data Generation, HTTP Execution Engine & Reporting Framework

**Standard Compliance:** IEEE 830 / ISO/IEC/IEEE 29148

---

## 1. Module Overview & Scope

This specification defines the functional and non-functional requirements for the core testing engine, covering test payload generation, HTTP execution handling, and report generation. The scope encompasses three main responsibilities:

1. **Test Data & Payload Generation:** Dynamically synthesizing valid, boundary (edge-case), and invalid (negative) payloads based on extracted OpenAPI schema constraints (`minimum`, `maximum`, `minLength`, `maxLength`, `pattern`, `enum`, `required`, data types).

2. **HTTP Test Execution & Resiliency:** Dispatching synthesized HTTP requests to target SUT endpoints, handling timeouts, connection retries, authentication headers, and enforcing rate limiting.

3. **Reporting & Defect Classification:** Aggregating test execution results, categorizing contract violations vs. server crashes, and exporting multi-format reports (JSON, HTML, JUnit XML) with reproducible cURL commands.

---

## 2. Functional Requirements (FR)

### 2.1. Test Data Generation

#### FR-GEN-01: Valid Payload Synthesis
* **Description:** The generation engine shall produce fully valid request bodies, query parameters, path variables, and headers that comply 100% with all declared schema rules and required fields.
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * **GIVEN** a valid OpenAPI parameter or request body schema,
  * **WHEN** valid payload generation is invoked,
  * **THEN** 100% of generated values satisfy all data type specifications, regex patterns, and range constraints without throwing schema validation errors on the client side.

#### FR-GEN-02: Boundary Value Payload Synthesis
* **Description:** The engine shall synthesize boundary (edge-case) payloads targeting exact schema limits, including $min$, $max$, $min - 1$, $max + 1$, $minLength$, $maxLength$, empty strings (`""`), empty arrays (`[]`), and zero values ($0$).
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * **GIVEN** a numeric constraint with $\text{minimum} = 10$ and $\text{maximum} = 100$,
  * **WHEN** boundary payload generation is executed,
  * **THEN** the engine generates test cases targeting exactly $10$ (lower boundary), $100$ (upper boundary), $9$ (off-by-one underflow), and $101$ (off-by-one overflow).

#### FR-GEN-03: Invalid & Negative Payload Synthesis
* **Description:** The engine shall generate negative test cases by intentionally violating schema constraints (e.g., data type mutation, enum value override, omitting required fields, injecting `null` into non-nullable fields).
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * **GIVEN** a required string field with an `enum: ["active", "pending"]`,
  * **WHEN** negative test generation is triggered,
  * **THEN** the system generates test variants with missing required fields, incorrect data types (e.g., integer `123` instead of string), and unlisted enum strings (e.g., `"INVALID_ENUM"`).

#### FR-GEN-04: Single Fault Isolation Principle
* **Description:** When synthesizing negative or boundary test cases, the generator shall mutate only one parameter/field per request while keeping all other fields fully valid.
* **Priority:** `Should Have` (Medium)
* **Acceptance Criterion:**
  * **GIVEN** a request schema with 5 parameters,
  * **WHEN** generating a negative test case for Parameter A,
  * **THEN** Parameters B, C, D, and E remain strictly valid, ensuring any server rejection is unequivocally attributable to Parameter A.

---

### 2.2. Test Execution Engine

#### FR-EXE-01: HTTP Request Dispatch & Header Injection
* **Description:** The execution engine shall construct and transmit HTTP requests (`GET`, `POST`, `PUT`, `DELETE`, `PATCH`) with appropriate `Content-Type` headers, path parameter substitutions, and user-configured authentication tokens (e.g., `Bearer <token>`, `X-API-Key`).
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * **GIVEN** a generated test payload and active target SUT base URL,
  * **WHEN** the test executor runs,
  * **THEN** the request is sent with resolved path variables (e.g., `/users/123`), valid headers, and correct body encoding (`application/json`).

#### FR-EXE-02: Configurable Timeout & Network Error Handling
* **Description:** The test executor shall enforce a user-configurable HTTP request timeout $T_{timeout}$ (default: $5.0\,\text{s}$) and gracefully handle socket dropouts, DNS failures, or connection refusals.
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * **GIVEN** an API endpoint that hangs or delays response beyond $T_{timeout} = 5.0\,\text{s}$,
  * **WHEN** the request is dispatched,
  * **THEN** the executor aborts the connection at $T = 5.0\,\text{s}$, marks the test case as `TIMED_OUT`, and seamlessly proceeds to the next test case.

#### FR-EXE-03: Rate Limiting & Concurrency Control
* **Description:** The executor shall allow setting maximum Requests Per Second (RPS) and worker concurrency limits to prevent accidentally overloading or Denial-of-Service (DoS) blocking the target SUT.
* **Priority:** `Should Have` (Medium)
* **Acceptance Criterion:**
  * **GIVEN** a concurrency limit configured to $N_{workers} = 5$ and rate limit $RPS_{max} = 50$,
  * **WHEN** running a suite of 500 tests,
  * **THEN** the engine maintains dispatch rate $\le 50\,\text{requests/sec}$ without dropping connections due to local rate-limiting queues.

---

### 2.3. Reporting Framework & Defect Export

#### FR-REP-01: Fault Categorization & Severity Mapping
* **Description:** The reporting module shall automatically group test results into defined failure taxonomy buckets: `500 Internal Server Error (Crash)`, `Undocumented Status Code`, `Schema Discrepancy`, `Timeout`, or `Passed`.
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * **GIVEN** a completed execution run of 200 test cases,
  * **WHEN** the report is generated,
  * **THEN** all failures are classified into the taxonomy, explicitly isolating HTTP 500 unhandled crashes from expected HTTP 400 validation rejections.

#### FR-REP-02: Reproducible cURL Command Export
* **Description:** For every failed or crashing test case, the report shall generate an exact, runnable `cURL` command string containing all headers, query parameters, and payload data.
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * **GIVEN** a test case that produced an HTTP `500` server crash,
  * **WHEN** viewing the test details in the report,
  * **THEN** a copyable `cURL` command is provided that reproduces the exact failure when executed independently in a CLI terminal.

#### FR-REP-03: Multi-Format Report Export (JSON, HTML, JUnit XML)
* **Description:** The reporting module shall export execution results in JSON (machine-readable raw data), standalone interactive HTML (dashboard for QA), and JUnit XML (for CI/CD pipeline integration).
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * **GIVEN** test suite completion in a CI/CD pipeline (e.g., GitHub Actions),
  * **WHEN** export is executed with `--format json,html,junit`,
  * **THEN** all three report files are generated on disk within $2.0\,\text{seconds}$, with valid XML syntax parsable by standard CI test report viewers.

---

## 3. Non-Functional Requirements (NFR)

### 3.1. Performance & Generation Throughput

#### NFR-PER-01: Data Generation Throughput
* **Description:** Payload synthesis must operate at high throughput to support large-scale API test suites.
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * The test data generation engine shall synthesize at least $1,000$ distinct boundary and invalid payload objects in time $T_{gen} \le 2.0\,\text{seconds}$ ($500\,\text{payloads/sec}$) on a single core $2.0\,\text{GHz}$ CPU.

#### NFR-PER-02: Report Build Latency
* **Description:** Generating HTML/JSON reports must not introduce significant latency at the end of test runs.
* **Priority:** `Should Have` (Medium)
* **Acceptance Criterion:**
  * Compiling and rendering an HTML dashboard report for a test run of $1,000$ executed requests shall complete in time $T_{report} \le 3.0\,\text{seconds}$.

---

### 3.2. Reliability & Resilience

#### NFR-REL-01: Execution Pipeline Fault Tolerance
* **Description:** Server crashes or unexpected connection drops on the target API must never cause the execution process to crash or corrupt report generation.
* **Priority:** `Must Have` (High)
* **Acceptance Criterion:**
  * **GIVEN** an SUT that crashes repeatedly and closes sockets abruptly on 50 consecutive requests,
  * **WHEN** running the test suite,
  * **THEN** the runner handles all network exceptions, records the errors, and produces a complete execution report without process termination.

---

### 3.3. Usability & Integrability

#### NFR-USA-01: Actionable Root-Cause Failure Inspection
* **Description:** The report output must provide clear visual diffs indicating exactly which boundary constraint or invalid parameter triggered the failure.
* **Priority:** `Should Have` (Medium)
* **Acceptance Criterion:**
  * **GIVEN** a test failure caused by string length overflow (`minLength: 5`, sent length `3`),
  * **WHEN** reviewing the HTML/JSON report,
  * **THEN** the report explicitly highlights the field path (`/user/bio`), target constraint (`minLength: 5`), and actual injected value (`"abc"`).

---

## 4. Requirements Traceability Matrix

| Requirement ID | Module / Component | Category | Priority | Target Verification Method |
|---|---|---|---|---|
| **FR-GEN-01** | Data Generator | Functional | Must Have | Automated Unit Test |
| **FR-GEN-02** | Data Generator | Functional | Must Have | Automated Boundary Unit Test |
| **FR-GEN-03** | Data Generator | Functional | Must Have | Automated Negative Unit Test |
| **FR-GEN-04** | Data Generator | Functional | Should Have | Automated Unit Test |
| **FR-EXE-01** | HTTP Executor | Functional | Must Have | Integration Test (Mock Server) |
| **FR-EXE-02** | HTTP Executor | Functional | Must Have | Timeout Integration Test |
| **FR-EXE-03** | HTTP Executor | Functional | Should Have | Load / Rate-limit Benchmark |
| **REP-01** | Reporting Engine | Functional | Must Have | System Integration Test |
| **FR-REP-02** | Reporting Engine | Functional | Must Have | Automated Verification Script |
| **FR-REP-03** | Reporting Engine | Functional | Must Have | CI/CD Integration Test |
| **NFR-PER-01** | Data Generator | Performance | Must Have | Performance Benchmark |
| **NFR-PER-02** | Reporting Engine | Performance | Should Have | Performance Profiling |
| **NFR-REL-01** | HTTP Executor | Reliability | Must Have | Fault Injection Stress Test |
| **NFR-USA-01** | Reporting Engine | Usability | Should Have | User Acceptance Inspection |
