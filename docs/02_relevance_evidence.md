## 5. Practitioner Interviews & Empirical Evidence

### 5.1. Interview Methodology & Demographic
* **Participants:** 3 Software Professionals (1 Backend Developer, 1 QA Automation Engineer, 1 DevOps Engineer).
* **Format:** Semi-structured 10-15 minute technical interviews focusing on REST API edge-case handling and OpenAPI contract testing.

---

### 5.2. Core Findings
1. **High Risk of Uncaught Exceptions (`500 Error`):** All respondents confirmed that APIs regularly return `500 Internal Server Error` instead of `400 Bad Request` when sent boundary-violating inputs.
2. **Manual Overhead:** Writing edge-case payloads in Postman or unit tests takes up to 25% of development time and is frequently skipped under tight deadlines.
3. **CI/CD Automation Need:** High interest in a lightweight CLI tool that automatically parses `openapi.yaml/json` and executes boundary checks before code merges.

---

### 5.3. Interview Summary Matrix

| Respondent Role | Key Question Asked | Primary Bottleneck Identified | Proposed Solution Impact |
| :--- | :--- | :--- | :--- |
| **Backend Developer** | How do you handle boundary inputs in JSON payloads? | Unhandled `500` errors due to missing type/length checks | Automatic boundary payload execution before PR merge |
| **QA Automation Engineer** | How much time goes into writing negative test suites? | High manual effort creating and maintaining Postman collections | Dynamic generation of test payloads directly from OpenAPI spec |
| **DevOps Engineer** | How is API contract testing integrated into CI/CD? | Lack of automated contract conformance checks in pipelines | CLI execution step inside GitHub Actions to block breaking builds |