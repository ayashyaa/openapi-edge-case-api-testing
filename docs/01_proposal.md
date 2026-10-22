# DIP-2: Project Proposal

## 1. Project Title
Automated API Contract Testing System with Edge-Case Payload Generation based on OpenAPI/Swagger Specifications.

## 2. Team & Project Leadership
* **Team Members:** Ayazhan Aetova, Assima Abdikarimova.
* **Supervisor:** Omirzak Islam.
* **Target Audience:** QA Engineers, Backend Developers, DevOps/CI-CD Pipelines.

## 3. Problem Statement
Manual test suite generation for edge-case boundaries in REST APIs is time-consuming, prone to human error, and rarely provides comprehensive test coverage. Existing automated tools and fuzzers often lack deep schema awareness, failing to enforce complex OpenAPI type constraints, enum values, and field boundaries.

## 4. Aim & Objectives
* **Aim:** Develop an automated contract testing system capable of parsing OpenAPI specifications, dynamically generating boundary and edge-case payloads, and validating HTTP responses against contract definitions.
* **Objectives:**
  1. Parse OpenAPI 3.0/3.1 (JSON/YAML) schemas into internal abstract representation models.
  2. Build a payload mutation engine for boundary conditions (numerical limits, string length bounds, null values, invalid enum options, and schema type mismatches).
  3. Execute automated HTTP requests against targeted backend servers.
  4. Validate response status codes, structure, and headers against expected contract rules.

## 5. Scope & Expected Deliverables
* **Scope:** RESTful HTTP APIs documented via OpenAPI 3.0/3.1 specifications.
* **Deliverable:** A runnable CLI application, core testing engine, and automated HTML/JSON report generator.