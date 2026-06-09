# Test Cases - Valentino Artisan Coffee House API

## API Under Test

| Field | Details |
|---|---|
| **API Name** | Valentino Artisan Coffee House API |
| **Base URL** | https://valentinos-coffee.herokuapp.com |
| **Documentation** | [Postman API Docs](https://www.postman.com/haris-itzon-2162036/api-automation-testing/documentation/79euqsq/valentino-artisan-coffee-house-api) |
| **Auth Method** | API Key passed as `x-api-key` request header (orders folder only) |
| **Test Tool** | Postman (`pm.test` assertions + JSON schema validation) |

---

## Coverage Summary

| Category | Count |
|---|---|
| Total Requests | 7 |
| Total `pm.test()` Assertions | 17 |
| All Passing | 17 |
| Endpoints Covered | 6 |
| Requests with JSON Schema Validation | 2 |
| Requests with Pre-request Scripts | 2 |

---

## Collection Variables Used

| Variable | Set By | Used By | Purpose |
|---|---|---|---|
| `baseUrl` | Hardcoded in collection | All requests | API base URL |
| `apiKey` | Hardcoded in collection | Orders folder (x-api-key header) | Authentication |
| `productId` | Hardcoded (`1004`) | GET /products/:productId | Target product for single product test |
| `customerName` | Pre-request script (`{{$randomFullName}}`) | POST /orders body and response assertion | Randomly generated customer name |
| `orderId` | Captured from POST /orders response via `pm.collectionVariables.set()` | GET /orders/:orderId | Chains order creation into single order retrieval |

---

## Test Cases

### Status

| TC ID | `pm.test` Name | Endpoint | Method | What Is Asserted | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|
| TC-001 | "Status code is 200" | /status | GET | `pm.response.to.have.status(200)` and `pm.expect(pm.response.code).to.eql(200)` | Status code 200 | 200 received | PASS |
| TC-002 | "Headers" | /status | GET | `pm.expect(pm.response.headers.get("X-Powered-By")).to.eql("Express")` | X-Powered-By header equals "Express" | "Express" returned | PASS |

---

### Products

#### GET /products

| TC ID | `pm.test` Name | Endpoint | Method | What Is Asserted | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|
| TC-003 | "Status code is 200" | /products | GET | `pm.response.to.have.status(200)` and `pm.expect(pm.response.code).to.eql(200)` | Status code 200 | 200 received | PASS |

#### GET /products/:productId (productId = 1004)

| TC ID | `pm.test` Name | Endpoint | Method | What Is Asserted | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|
| TC-004 | "Status code is 200" | /products/1004 | GET | `pm.response.to.have.status(200)` and `pm.expect(pm.response.code).to.eql(200)` | Status code 200 | 200 received | PASS |
| TC-005 | "Response body is JSON" | /products/1004 | GET | `pm.response.to.be.json` | Response content-type is JSON | JSON response confirmed | PASS |
| TC-006 | "Product id and name match" | /products/1004 | GET | `pm.expect(response.id).to.eql(parseInt(pm.collectionVariables.get("productId")))` and `pm.expect(response).to.have.property("name")` | `id` equals 1004 (integer) and `name` property exists | id: 1004, name: "Americano" | PASS |
| TC-007 | "Schema is valid" | /products/1004 | GET | Full JSON schema validation: `id` (number), `category` (string), `name` (string), `isAvailable` (boolean), `product-description` (string), `additionalText` (string) — all required, `additionalProperties: false` | Schema matches; all required fields present with correct types | All fields present: id: 1004, category: "coffee", name: "Americano", isAvailable: true, product-description: "A strong and bold coffee made with a shot of espresso and hot water.", additionalText: "Contains caffeine" | PASS |

---

### Clients

| TC ID | `pm.test` Name | Endpoint | Method | What Is Asserted | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|
| TC-008 | "Status code is 200" | /clients | POST | `pm.response.to.have.status(200)` and `pm.expect(pm.response.code).to.eql(200)` | Status code 200 | 200 received | PASS |

> **Note:** Request body uses Postman dynamic variable `{{$randomExampleEmail}}` to generate a unique email per run, avoiding duplicate registration conflicts.

---

### Orders

#### POST /orders

> **Pre-request script:** `pm.collectionVariables.set("customerName", pm.variables.replaceIn("{{$randomFullName}}"))` generates a random customer name before each request.

| TC ID | `pm.test` Name | Endpoint | Method | What Is Asserted | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|
| TC-009 | "Response body is JSON" | /orders | POST | `pm.response.to.be.json`; also captures `response.id` into `orderId` collection variable via `pm.collectionVariables.set("orderId", response.id)` | JSON response; `orderId` variable set for use in GET /orders/:orderId | JSON confirmed; orderId captured and chained | PASS |
| TC-010 | "Status code is 201" | /orders | POST | `pm.response.to.have.status(201)` and `pm.expect(pm.response.code).to.eql(201)` | Status code 201 | 201 received | PASS |
| TC-011 | "Customer Name" | /orders | POST | `pm.expect(response.customerName).to.eql(pm.collectionVariables.get("customerName"))` | `customerName` in response matches the value set in pre-request script | Customer name in response matched pre-generated value | PASS |
| TC-012 | "Schema is valid" | /orders | POST | Full JSON schema validation: `id` (string), `clientId` (string), `created` (string, format: date-time), `customerName` (string), `products` (array of objects with `id`: integer and `quantity`: integer) — all required, `additionalProperties: false` | Schema matches; all required fields present with correct types | All fields present and correctly typed per saved response | PASS |

#### GET /orders

| TC ID | `pm.test` Name | Endpoint | Method | What Is Asserted | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|
| TC-013 | "Status code is 200" | /orders | GET | `pm.response.to.have.status(200)` and `pm.expect(pm.response.code).to.eql(200)` | Status code 200 | 200 received | PASS |

#### GET /orders/:orderId

> **Variable chaining:** `orderId` used here is the value captured from the `POST /orders` response in TC-009. This creates an end-to-end flow: create order → retrieve that specific order by ID.

| TC ID | `pm.test` Name | Endpoint | Method | What Is Asserted | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|
| TC-014 | "Response is in JSON" | /orders/:orderId | GET | `pm.response.to.be.json` | Response content-type is JSON | JSON response confirmed | PASS |
| TC-015 | "Status code is 200" | /orders/:orderId | GET | `pm.response.to.have.status(200)` and `pm.expect(pm.response.code).to.eql(200)` | Status code 200 | 200 received | PASS |
| TC-016 | "Id exists" | /orders/:orderId | GET | `pm.expect(response).to.have.property("id")` | `id` property exists in response | `id` property present | PASS |
| TC-017 | "Products property exists and is an array" | /orders/:orderId | GET | `pm.expect(response).to.have.property("products")` and `pm.expect(response.products).to.be.an("array")` | `products` property exists and is an array type | `products` present as array | PASS |

---

## Observed API Behavior

### OBS-001 — Order ID Format Inconsistency

| Field | Details |
|---|---|
| **Endpoint** | `POST /orders` |
| **Related Test** | TC-009, TC-012 |
| **Severity** | Medium |
| **Status** | Open |
| **Reproducibility** | Intermittent |
| **Description** | The `id` field in the order creation response does not consistently follow the expected 9-character uppercase alphanumeric format. During testing, the API was observed to intermittently return non-conforming IDs. |
| **Expected Format** | 9 uppercase alphanumeric characters — e.g. `YKTGRCCON` |
| **Observed** | Occasional non-conforming IDs returned on certain runs |
| **Root Cause** | API-side ID generation inconsistency |
| **Impact** | Any client relying on a predictable ID format for lookups or storage would receive malformed data |
| **Workaround** | Re-run the collection until a correctly formatted order ID is returned |
