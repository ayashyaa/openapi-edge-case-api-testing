# Project Proposal and Scope: OpenAPI Edge Tester

**Diploma project,** Astana IT University, School of Software Engineering
---

## 1. Project Title

* **English:** Development of an automated API contract testing system that generates test payloads for edge cases based on OpenAPI/Swagger specifications
* **Русский:** Разработка системы автоматизированного тестирования контрактных API, генерирующей тестовые данные для краевых случаев на основе спецификаций OpenAPI/Swagger
* **Қазақша:** OpenAPI/Swagger спецификациялары негізінде шекті жағдайлар (edge cases) үшін тестілік деректерді генерациялайтын келісімшарттық API-ді автоматтандырылған тестілеу жүйесін әзірлеу

---
## 2. Team and Supervisor

| Member | Group | Responsibilities |
| :--- | :--- | :--- |
| **Abdikarimova Asima Tileukabylovna** | SE-2404 | Loading and parsing of OpenAPI/Swagger specifications; extraction of endpoints, HTTP methods, request and response schemas; validation of API responses against the contract; detection of contract violations |
| **Ayetova Ayazhan Sayatovna** | SE-2404 | Analysis of schema constraints and data types; generation of valid, boundary and invalid test data and JSON payloads; execution of test requests; collection of responses; report of found problems |

**Supervisor:** Islam Omirzak

---

## 3. Problem Statement

REST APIs are commonly described by OpenAPI/Swagger specifications, which define the data types and constraints of every request and response (`minLength`, `maxLength`, `minimum`, `maximum`, `pattern`, `enum`, `required`, and others). In practice, tests for such APIs are often written by hand and focus on typical input values. Boundary values (for example, a string exactly at its maximum length), values just outside the allowed range, missing required fields and wrongly typed data are tested less systematically.

Untested edge cases can cause a service to fail in unexpected ways. A typical example is a server that returns `500 Internal Server Error` for malformed input instead of a correct validation response such as `400 Bad Request`. Another problem is that real responses may not match the contract described in the specification.

Writing and maintaining edge-case tests manually for every endpoint and every field is slow and error-prone. The specification already contains the information needed to produce such tests automatically, but this information is not always fully used.


---

## 4. Target Users

* **QA engineers and test automation engineers** who test REST APIs
* **Backend developers** who maintain REST APIs and want to check them against their own specification
* **Development teams** that use OpenAPI/Swagger specifications as an API contract

---
## 5. Aim

To develop a system that automatically generates edge-case test payloads from OpenAPI/Swagger specifications and uses them to test REST APIs for contract compliance and robustness.

---
## 6. Objectives

1. Analyze existing approaches and tools for API testing, contract testing, and test data generation and compare existing systems.
2. Collect and specify functional and non-functional requirements with verifiable acceptance criteria.
3. Design the system architecture (system context, use cases, components).
4. Develop a module that loads and parses OpenAPI/Swagger specifications and extracts endpoints, HTTP methods and request and response schemas.
5. Analyze schema constraints and design strategies for generating valid, boundary and invalid test data.
6. Develop a test data generator that produces valid, boundary and invalid JSON payloads.
7. Develop a module that sends the generated requests to the tested API, tracks responses, and handles timeouts and connection failures.
8. Develop a module that validates API responses against the contract and detects contract violations.
9. Develop a report module that collects test results, classifies detected anomalies (for example, 500 instead of 400) and exports the report.
10. Evaluate the system on test REST APIs and compare the results with a baseline solution.

---
## 7. Expected Outcomes

* A parser for OpenAPI/Swagger specifications
* A test data generator for valid, boundary and invalid JSON payloads
* A module for executing test requests
* A contract validator for API responses
* A report of detected problems with classification of anomalies
* Source code in a Git repository with a traceable development history
* Automated tests
* An evaluation report with results on test REST APIs
* Project documentation: literature review, comparisons, requirements, architecture and a verification plan

---
## 8. Project Scope

### In Scope
* REST APIs described by OpenAPI/Swagger specifications
* JSON request and response bodies
* Analysis of schema constraints and data types from the specification
* Generation of valid, boundary and invalid test data based on these constraints
* Sending generated requests to a tested service and collecting responses
* Validation of responses against the contract
* Classification of detected problems and generation of a report
* Evaluation on test REST APIs and comparison with a baseline solution

### Out of Scope
* Protocols other than REST (for example, GraphQL and gRPC)
* Full load and performance testing
* A graphical user interface (the system is used through the command line and configuration files)
* Automatic fixing of the tested API
* APIs that are not described by an OpenAPI/Swagger specification
---

## 9. Engineering Complexity

The project involves several non-trivial engineering tasks:
* Parsing specifications and resolving references, nested objects and compositions of schemas
* Algorithms for generating boundary and invalid values from different kinds of constraints and their combinations
* Generating structured JSON payloads that stay consistent with the schema
* Reliable execution of test requests with timeouts and failure handling
* Comparing real responses with the contract and classifying anomalies
* Integration of two independently developed parts (parsing and validation, generation and execution) into a single system

---

## 10. Verification of the Aim

Achievement of the aim will be shown by the working prototype, automated tests and an evaluation on test REST APIs. The evaluation will use defined metrics and a baseline solution, which are described in the verification and evaluation plan.

---
## 11. Project Management

* **Git repository:** [https://github.com/ayashyaa/openapi-edge-case-api-testing](https://github.com/ayashyaa/openapi-edge-case-api-testing)
* **YouTrack project:** [https://openapi-edge-tester.youtrack.cloud](https://openapi-edge-tester.youtrack.cloud)
* Every work item is tracked as a YouTrack task (`DIP-N`), linked to branches, commits and pull requests
