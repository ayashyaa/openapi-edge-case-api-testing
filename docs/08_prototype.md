# DIP-12: Prototype Materials & CLI Usage Examples

## 1. Overview
This document contains usage examples, sample OpenAPI input specifications, generated edge-case payload formats, and execution report structures for the Automated API Contract Testing System prototype.

---

## 2. CLI Execution Examples

### 2.1. Run Full Edge-Case Contract Test
```bash
python -m openapi_edge_tester run --spec config/petstore_sample.json --target http://localhost:8000
```

### 2.2. Generate Edge-Case Payloads Only (Dry-Run)
```bash
python -m openapi_edge_tester generate --spec config/petstore_sample.json --out config/generated_payloads.json

```

### 2.3. Export Execution Summary Report
```bash
python -m openapi_edge_tester report --input config/execution_report.json --format html --out report.html
```

---

## 3. Prototype Artifact References
Sample OpenAPI Spec: config/petstore_sample.json
Generated Edge-Case Payloads Sample: config/generated_payloads.json
Execution Report Sample: config/execution_report.json
