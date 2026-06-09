# API Test Automation - Valentino Artisan Coffee House

[![Automated API tests using Postman CLI](https://github.com/haris-yousaf/Api-Testing/actions/workflows/postman.yml/badge.svg)](https://github.com/haris-yousaf/Api-Testing/actions/workflows/postman.yml)

## Overview

This project contains a fully automated test suite for the **Valentino Artisan Coffee House REST API** — a coffee order and product management system. Tests were designed and implemented in Postman with `pm.test` assertions and JSON schema validation, automated via Newman CLI, and integrated into a GitHub Actions CI/CD pipeline that executes on every push to `main`.

---

## API Under Test

| Field | Details |
|---|---|
| **API Name** | Valentino Artisan Coffee House API |
| **Base URL** | https://valentinos-coffee.herokuapp.com |
| **Domain** | Coffee shop order and product management |
| **Documentation** | [Postman API Docs](https://www.postman.com/haris-itzon-2162036/api-automation-testing/documentation/79euqsq/valentino-artisan-coffee-house-api) |
| **Auth Method** | API Key passed as `x-api-key` request header (scoped to orders folder) |

---

## Tools and Technologies

| Tool | Purpose |
|---|---|
| Postman | Collection authoring, `pm.test` script writing, JSON schema validation, manual execution |
| Newman | CLI runner for headless and automated collection execution |
| Newman HTMLextra | Detailed HTML test report generation |
| GitHub Actions | CI/CD pipeline — automated test execution on every push to `main` |

---

## Test Coverage

| Category | Count |
|---|---|
| Total Requests | 7 |
| Total Test Cases (`pm.test` assertions) | 17 |
| Passing Test Cases | 17 |
| Endpoints Covered | 6 |
| HTTP Methods Covered | GET, POST |
| Folders with Auth | 1 (orders — API Key via `x-api-key` header) |

---

## Endpoints Tested

| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| GET | /status | No | API health check |
| GET | /products | No | Retrieve all products |
| GET | /products/:productId | No | Retrieve a single product by ID |
| POST | /clients | No | Register a new API client |
| POST | /orders | Yes | Create a new order |
| GET | /orders | Yes | Retrieve all orders |
| GET | /orders/:orderId | Yes | Retrieve a specific order by ID |

---

## What Was Tested

- **API Health and Headers:** Status endpoint availability, response body structure, and `X-Powered-By` response header validation
- **Product Retrieval:** Full product list and individual product lookup by ID (product 1004 — Americano)
- **JSON Schema Validation:** Full schema validation on both `GET /products/:productId` and `POST /orders` responses, validating field names, data types, required fields, and `additionalProperties: false`
- **Client Registration:** Valid registration with a dynamically generated random email using Postman's `{{$randomExampleEmail}}` dynamic variable
- **Order Management:** Order creation with multiple products, order list retrieval, and single order lookup by ID
- **Dynamic Data Generation:** Pre-request script generates a random customer name using `{{$randomFullName}}` before each order creation request
- **Collection Variable Chaining:** `customerName` is set in the pre-request script and validated in the response body; `orderId` is captured from the `POST /orders` response via `pm.collectionVariables.set()` and automatically injected into the `GET /orders/:orderId` request

---

## Test Cases

Full test case documentation with exact `pm.test` names, assertions, and results:
[docs/test-cases.md](./docs/test-cases.md)

---

## How to Run Locally

### Prerequisites

```bash
npm install -g newman
npm install -g newman-reporter-htmlextra
```

### Run the Collection

```bash
newman run postman/collection.json \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export reports/test-report.html
```

### Output

Results display in the terminal. A detailed HTML report is saved to `reports/test-report.html` — open it in any browser.

---

## CI/CD Pipeline

Tests execute automatically on every push to the `main` branch via GitHub Actions.

| Field | Details |
|---|---|
| **Workflow File** | `.github/workflows/postman.yml` |
| **Trigger** | Push to `main` branch |
| **Runner** | `ubuntu-latest` |
| **View Runs** | [Actions Tab](https://github.com/haris-yousaf/Api-Testing/actions) |

---

## Observed API Behavior

| # | Endpoint | Method | Observation | Severity | Status |
|---|---|---|---|---|---|
| OBS-001 | /orders | POST | Order `id` field intermittently returns values that do not conform to the expected 9-character uppercase alphanumeric format (e.g. `YKTGRCCON`). Non-conforming IDs were observed during testing on certain runs, indicating inconsistent ID generation on the API side. | Medium | Open |

---

## Repository Structure

```
Api-Testing/
├── .github/
│   └── workflows/
│       └── postman.yml          # CI/CD pipeline definition
├── postman/
│   └── collection.json          # Postman collection (7 requests, 17 pm.test assertions)
├── docs/
│   └── test-cases.md            # Full test case documentation
├── reports/
│   └── test-report.html         # Sample Newman HTMLextra report
└── README.md
```

> **Note:** The environment file is not committed to this repository as it contains credential values. Collection variables are used for runtime data (`orderId`, `customerName`) and the API key is managed separately.

---

## Author

Haris Yousaf — SQA Engineer  
[LinkedIn](https://www.linkedin.com/in/haris-yousaf-dev) | [GitHub](https://github.com/haris-yousaf)
