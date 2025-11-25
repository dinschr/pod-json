# Order API Concept (POD-JSON Lite)

This document describes a **conceptual REST-style HTTP API** that uses POD-JSON Lite as its order payload.  
A REST API means the system communicates using standard HTTP methods (GET, POST, etc.) and JSON request/response bodies, making integration simple and predictable for machines.

The purpose of this API is to define a clear workflow for integrators to:

- discover products and SKUs,
- check stock,
- submit orders,
- read order statuses.

> **Note:** This document is conceptual and may not match any live implementation 1:1.  
> Exact URLs and behaviour will be agreed with integrators during onboarding.

---

## 1. Authentication

All endpoints (except possibly `/health`) require authentication using a **Bearer token**.

Each integration partner receives one or more tokens from Dinschrift.

### 1.1. Request header

```http
Authorization: Bearer <token>
```

Tokens are:

- long, opaque strings  
- bound to a specific partner account  
- revocable by Dinschrift at any time

There is no public sign-up at this stage; access is granted manually by Dinschrift.

---

## 2. Base URL

For illustration, we assume a base URL:

```text
https://api.dinschrift.ch
```

All paths below are relative to this base.

Example:

- `GET /v1/health`
- `POST /v1/orders`

The actual base URL for test and production environments will be communicated directly to integrators.

---

## 3. Health check

### 3.1. `GET /v1/health`

Simple check to see if the API is reachable and healthy.

- **Auth:** not required (may be open)
- **Request body:** none
- **Response:** simple JSON object

Example response:

```json
{
  "status": "ok",
  "service": "dinschrift-order-api",
  "time": "2025-01-01T12:00:00Z"
}
```

---

## 4. Product & SKU endpoints

These endpoints allow integrators to discover which products and SKUs they can order.

### 4.1. `GET /v1/products`

Returns a list of products available to the authenticated partner.

- **Auth:** required  
- **Query params:** none (initial version)  
- **Response:** list of simplified product objects

Example response (conceptual):

```json
{
  "products": [
    {
      "productId": "STTU758",
      "name": "Stanley/Stella Creator",
      "brand": "Stanley/Stella",
      "category": "T-Shirt",
      "description": "Unisex medium fit single jersey t-shirt",
      "allowedSides": ["front", "back"],
      "availableSkus": [
        "STTU758_C001_S",
        "STTU758_C001_M",
        "STTU758_C001_L"
      ]
    }
  ]
}
```

The exact product fields may be adjusted based on Dinschrift’s catalogue structure.

---

### 4.2. `GET /v1/skus/{sku}`

Returns details about a specific SKU.

- **Auth:** required  
- **Path parameter:** `sku` – the SKU code.  
- **Response:** SKU details.

Example response (conceptual):

```json
{
  "sku": "STTU758_C001_M",
  "productId": "STTU758",
  "size": "M",
  "colourCode": "C001",
  "colourName": "Off White",
  "allowedSides": ["front", "back"],
  "defaultProfile": "LGT"
}
```

---

## 5. Stock endpoints

These endpoints allow integrators to check stock before creating orders.

### 5.1. `GET /v1/stock`

Check stock for a single SKU.

- **Auth:** required  
- **Query parameters:**
  - `sku` (required) – SKU code.
  - `warehouse` (optional) – warehouse/location code, if applicable.

Example request:

```http
GET /v1/stock?sku=STTU758_C001_M HTTP/1.1
Authorization: Bearer <token>
```

Example response:

```json
{
  "sku": "STTU758_C001_M",
  "available": 34,
  "backorderAllowed": false,
  "estimatedLeadTimeDays": 2
}
```

Field meanings:

- `available` – current stock that can be reserved for orders.
- `backorderAllowed` – whether ordering beyond current stock is allowed.
- `estimatedLeadTimeDays` – indicative lead time, for information only.

---

### 5.2. `POST /v1/stock/query`

Batch stock query for multiple SKUs.

- **Auth:** required  
- **Request body:**

```json
{
  "skus": ["STTU758_C001_S", "STTU758_C001_M", "STTU758_C001_L"],
  "warehouse": "CH"
}
```

- **Response body:**

```json
{
  "results": [
    {
      "sku": "STTU758_C001_S",
      "available": 10,
      "backorderAllowed": false,
      "estimatedLeadTimeDays": 2
    },
    {
      "sku": "STTU758_C001_M",
      "available": 34,
      "backorderAllowed": false,
      "estimatedLeadTimeDays": 2
    },
    {
      "sku": "STTU758_C001_L",
      "available": 0,
      "backorderAllowed": true,
      "estimatedLeadTimeDays": 7
    }
  ]
}
```

---

## 6. Orders

The main purpose of the API is to accept orders in POD-JSON Lite format and provide status updates.

### 6.1. `POST /v1/orders`

Submit a new order.

- **Auth:** required  
- **Headers:**
  - `Authorization: Bearer <token>`
  - `Content-Type: application/json`
  - Optional: `Idempotency-Key: <uuid>` – to avoid duplicate orders on retry.

- **Request body:**  
  A single [POD-JSON Lite Order](./pod-json-lite.md).

- **Successful response (example):**

```json
{
  "orderId": "PARTNER-12345",
  "apiOrderId": "DINSCHRIFT-98765",
  "status": "ACCEPTED",
  "receivedAt": "2025-01-01T12:00:00Z"
}
```

Where:

- `orderId` – the integrator’s order ID from the request.
- `apiOrderId` – Dinschrift’s internal order identifier.
- `status` – initial status, usually `ACCEPTED` or `REJECTED`.
- `receivedAt` – timestamp when the API accepted the order.

If the request is invalid or cannot be processed, an error response is returned (see [Error model](#7-error-model)).

---

### 6.2. Idempotency

To avoid duplicate orders when retrying a request, integrators may send an `Idempotency-Key` header:

```http
Idempotency-Key: 9a0fe5df-3b5a-4a76-b29b-0c317f8fa999
```

If the same partner sends multiple `POST /v1/orders` requests with the **same** Idempotency-Key:

- The first valid request creates the order.
- Subsequent requests with the same key return the **original** result (same `apiOrderId`, same status), as long as the body is identical or compatible.

The exact behaviour and retention window should be documented in the live implementation.

---

### 6.3. `GET /v1/orders/{apiOrderId}`

Fetch details and status of an order.

- **Auth:** required  
- **Path parameter:** `apiOrderId` – the order ID returned by `POST /v1/orders`.

- **Response body (example):**

```json
{
  "apiOrderId": "DINSCHRIFT-98765",
  "orderId": "PARTNER-12345",
  "status": "IN_PRODUCTION",
  "createdAt": "2025-01-01T12:00:00Z",
  "updatedAt": "2025-01-02T09:30:00Z",
  "tracking": {
    "carrier": "SwissPost",
    "service": "PostPac Priority",
    "trackingNumber": "99.00.123456.12345678",
    "trackingUrl": "https://service.post.ch/track/99.00.123456.12345678"
  },
  "order": {
    "schemaVersion": "pod-json-lite-0.1",
    "orderId": "PARTNER-12345",
    "externalRef": "SHOP-CART-789",
    "currency": "CHF",
    "shippingMethod": "POST_PRIORITY",
    "shippingAddress": {
      "firstName": "Anna",
      "lastName": "Muster",
      "company": "Muster GmbH",
      "street1": "Beispielstrasse 1",
      "street2": "",
      "postalCode": "8000",
      "city": "Zuerich",
      "countryCode": "CH",
      "phone": "+41441234567",
      "email": "anna@example.com"
    },
    "lines": [
      {
        "lineId": "1",
        "sku": "STTU758_C001_M",
        "quantity": 2,
        "print": {
          "side": "front",
          "profile": "LGT",
          "designUrl": "https://cdn.partner.com/files/design-1234.png"
        }
      }
    ]
  }
}
```

#### 6.3.1. Status values

Initial set of machine-readable status values:

- `PENDING` – received, validation in progress  
- `ACCEPTED` – validated and queued for production  
- `IN_PRODUCTION` – currently being printed/processed  
- `SHIPPED` – shipped to the shipping address  
- `CANCELLED` – cancelled (with reason)  
- `ERROR` – permanent error that prevents completion (with reason)

More detailed sub-statuses may be added later if needed.

---

## 7. Error model

All error responses use a common structure and appropriate HTTP status codes.

### 7.1. Error response format

```json
{
  "error": {
    "code": "INVALID_POD_JSON",
    "message": "The order body could not be validated.",
    "details": {
      "field": "lines[0].sku",
      "reason": "Unknown SKU for this account."
    }
  }
}
```

### 7.2. Example error codes

- `AUTH_FAILED`  
  Invalid or missing token.

- `FORBIDDEN`  
  Token is valid, but the operation is not allowed for this account.

- `INVALID_POD_JSON`  
  Order body does not conform to POD-JSON Lite specification.

- `INVALID_SKU`  
  SKU is not valid or not available for this partner.

- `OUT_OF_STOCK`  
  Requested quantity exceeds available stock and backorder is not allowed.

- `RATE_LIMITED`  
  Too many requests in a given time.

- `INTERNAL_ERROR`  
  Unexpected error on the server side.

A common mapping pattern is:

- `400` – invalid request / invalid POD-JSON  
- `401` – authentication failed  
- `403` – forbidden  
- `404` – not found  
- `409` – conflict (e.g. duplicate orderId)  
- `429` – rate limit  
- `500` – internal server error  

---

## 8. Payments and billing

The Order API itself **does not process payments**.

Commercial and financial aspects are handled separately, for example:

- **Post-paid / invoice:**  
  Dinschrift invoices the integrator for all completed orders in a period.

- **Pre-paid / credit:**  
  Integrator maintains a credit balance; new orders are accepted only if the balance is sufficient.

The API may expose informational endpoints in the future (e.g. `GET /v1/account/balance`), but this is out of scope for the initial concept.

The only requirement for the API is:

> It must be able to decide whether an order is allowed to be accepted (e.g. account active, credit OK).

---

## 9. Webhooks (optional, future)

In addition to polling `GET /v1/orders/{id}`, the API may support **webhooks** in the future:

- Integrator provides a `callbackUrl` at account level or per order.
- Dinschrift sends HTTP `POST` requests to that URL on significant events:
  - order accepted
  - status changed to `IN_PRODUCTION`, `SHIPPED`, etc.

Webhook design is outside the scope of this initial concept but can be added in a future version of this document.
