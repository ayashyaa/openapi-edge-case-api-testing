# DIP-6: Existing Systems Comparison

## 1. Overview
This document presents a comparative analysis of existing API testing and specification-driven tools (Schemathesis, RESTler, Dredd, and Postman/Newman) against the proposed Automated Edge-Case API Testing System.

---

## 2. Feature Comparison Matrix

| Features / Criteria | Schemathesis | RESTler | Dredd | Postman / Newman | Proposed System |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **OpenAPI 3.0/3.1 Support** | Full | Partial (3.0) | Partial (2.0/3.0) | Manual Import | Full |
| **Edge-Case Payload Generation** | Dynamic (PBT) | Stateful Fuzzing | No (Examples only) | Manual scripts | Automated Boundary & Type Mutation |
| **Boundary Value Analysis (BVA)** | Medium | Low | None | Manual | High (Dedicated Engine) |
| **Contract Conformance Checks** | Yes | Yes | Yes | Manual assertions | Automated (Status & Schema) |
| **CLI & CI/CD Integration** | Yes | Yes | Yes | Yes | Yes |
| **Reporting Formats** | Terminal / HTML | Log files | Terminal | HTML / JUnit | Terminal / HTML / JSON |

---

## 3. Analysis of Existing Solutions

1. **Schemathesis:** Excellent for property-based testing, but requires manual configuration for deep boundary constraints and localized error tracing.
2. **RESTler:** Focuses primarily on stateful sequence fuzzing rather than exhaustive boundary value analysis for specific parameter constraints (`minLength`, `maximum`, `enum`).
3. **Dredd:** Validates API responses strictly against documented examples in the specification, lacking dynamic negative test payload generation.
4. **Postman / Newman:** Widely used for regression and happy-path testing, but relies heavily on manually written test scripts for edge cases.

---

## 4. Gap Justification for Proposed Project
While current tools provide general fuzzing or basic schema verification, they often fail to systematically exercise boundary conditions (`min - 1`, `max + 1`, unexpected nulls, type mismatches) defined in OpenAPI contracts. The proposed system bridges this gap by offering a lightweight, automated engine specifically designed for boundary-value and contract-violation testing.