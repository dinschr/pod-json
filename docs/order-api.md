# Order API Concept (POD-JSON Lite)

This document describes a **conceptual REST-style HTTP API** that uses POD-JSON Lite as its order payload.  
A REST API means the system communicates using standard HTTP methods (GET, POST, etc.) and JSON request/response bodies, making integration simple and predictable for machines.

The goal is to define a clear, predictable way for an integrator’s system to:

- discover products and SKUs,
- check stock,
- submit orders,
- read order statuses.

> **Note:** This document is conceptual and may not match any live implementation 1:1.  
> Exact URLs and behaviour will be agreed with integrators during onboarding.

---

## 0. Implementation status

This section reflects the current state of the private pilot implementation.

- `GET /v1/health` – ✅ Live  
- `GET /v1/orders/{clientId}` – ⚙️ Designed, implementation in progress  
- `POST /v1/orders` – ⚙️ Designed, implementation in progress  
- `GET /v1/orders/{apiOrderId}` – 💤 Planned  
- Product & SKU endpoints – 💤 Conceptual (not yet implemented)  
- Webhooks – 💤 Planned  

The wire protocol and JSON formats are considered **stable enough for pilot use**, but details may still change.

---

## 1. Authentication

All endpoints (except `/health`) require authentication using a **Bearer token**.

Each integration partner receives one or more tokens from Dinschrift via their account log-in.

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
- `GET /v1/orders/{clientId}`
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

> **Status:** conceptual – details will be finalised once the pilot focuses on external product catalogues.

### 4.1. `GET /v1/products`

Returns a **compact list of product styles** (models) available to the authenticated partner.  
This endpoint is intended for building product pickers such as “all T-Shirts”, “all hoodies”, “all pullovers”, etc.

- **Auth:** required  
- **Query params (optional):**
  - `category` – filter by product category, e.g. `tshirt`, `pullover`, `hoodie`.
  - `brand` – filter by brand, e.g. `Stanley/Stella`.
  - `search` – simple text search on product name/description.

- **Response:** list of product-level objects (no SKUs in this endpoint).

#### Example response

```json
{
  "products": [
    {
      "productId": "STTU758",
      "name": "Creator",
      "brand": "Stanley/Stella",
      "category": "tshirt",
      "description": "Unisex medium fit single jersey T-shirt",
      "allowedSides": ["front", "back"],
      "printProfiles": ["LGT", "DRK", "NOTRANS"]
    },
    {
      "productId": "STSU822",
      "name": "Cruiser",
      "brand": "Stanley/Stella",
      "category": "pullover",
      "description": "Unisex crew neck sweatshirt",
      "allowedSides": ["front", "back"]
    }
  ]
}
```

#### Product-level fields

| Field           | Type   | Description                                            |
|-----------------|--------|--------------------------------------------------------|
| `productId`     | string | Internal product code (e.g. `STTU758`).               |
| `name`          | string | Marketing/product name.                               |
| `brand`         | string | Brand (e.g. `Stanley/Stella`).                        |
| `category`      | string | Normalised category (`tshirt`, `pullover`, etc.).     |
| `description`   | string | Optional human-readable description.                  |
| `allowedSides`  | array  | Which sides the product can be printed on.            |
| `printProfiles` | array  | Supported profiles, e.g. `["LGT","DRK","NOTRANS"]`.   |

> **Note:** This endpoint intentionally does **not** return SKUs to keep the response small and fast.  
> SKUs are returned via `GET /v1/products/{productId}` and `GET /v1/skus`.

---

### 4.2. `GET /v1/products/{productId}`

Returns a **single product**, including all SKUs for that style that are available to the authenticated partner.

- **Auth:** required  
- **Path parameter:** `productId` – e.g. `STTU758`.

#### Example response

```json
{
  "productId": "STTU758",
  "name": "Creator",
  "brand": "Stanley/Stella",
  "category": "tshirt",
  "description": "Unisex medium fit single jersey T-shirt",
  "allowedSides": ["front", "back"],
  "printProfiles": ["LGT", "DRK", "NOTRANS"],
  "skus": [
    {
      "sku": "STTU758_C001_S",
      "colorCode": "C001",
      "colorName": "Off White",
      "colorGroup": "white",
      "hex": "#f7f3ef",
      "size": "S",
      "image": "https://img.dinschrift.ch/packshots/STTU758_C001_S.jpg",
      "price": {
        "unitPrice": 12.50,
        "currency": "CHF"
      }
    },
    {
      "sku": "STTU758_C318_M",
      "colorCode": "C318",
      "colorName": "Red",
      "colorGroup": "red",
      "hex": "#d2232a",
      "size": "M",
      "image": "https://img.dinschrift.ch/packshots/STTU758_C318_M.jpg",
      "price": {
        "unitPrice": 13.20,
        "currency": "CHF"
      }
    }
  ]
}
```

#### SKU-level fields (for this endpoint)

| Field             | Type    | Description                                           |
|-------------------|---------|-------------------------------------------------------|
| `sku`             | string  | Unique SKU code: `PRODUCT_COLOUR_SIZE`.              |
| `colorCode`       | string  | Internal colour code (e.g. `C318`).                  |
| `colorName`       | string  | Human-friendly colour name (e.g. `Red`).             |
| `colorGroup`      | string  | Normalised colour family (`red`, `blue`, `black`).   |
| `hex`             | string  | HEX colour value for UI preview.                     |
| `size`            | string  | Variant size (`S`, `M`, `L`, etc.).                  |
| `image`           | string  | URL to the packshot for that SKU.                    |
| `price.unitPrice` | number  | Partner-specific unit price (garment + print).       |
| `price.currency`  | string  | ISO currency code, e.g. `CHF`, `EUR`.                |

> **Note:** Prices and available SKUs are **partner-specific** and depend on the authenticated account.

---

### 4.3. `GET /v1/skus`

Search and browse **orderable SKUs** for the authenticated partner.  
This is the main machine endpoint for questions like:

- “All red T-shirts”  
- “All pullovers”  
- “All hoodies in size M”  

- **Auth:** required  
- **Query parameters (all optional, used for filtering):**
  - `productId` – limit to a specific product (e.g. `STTU758`).
  - `category` – `tshirt`, `pullover`, `hoodie`, etc.
  - `color` – normalised colour group, e.g. `red`, `black`, `white`.
  - `colorCode` – exact internal colour code, e.g. `C318`.
  - `size` – `XS`, `S`, `M`, `L`, `XL`, etc.
  - `brand` – brand filter, e.g. `Stanley/Stella`.
  - `limit` – max number of SKUs per page (e.g. `100` or `200`).
  - `cursor` – pagination cursor returned by a previous response.

If no filters are provided, the API may apply a default limit and return the **first page** of all SKUs available to that partner.

#### Example: all red T-shirts

```http
GET /v1/skus?category=tshirt&color=red&limit=200 HTTP/1.1
Authorization: Bearer <token>
```

#### Example: all pullovers

```http
GET /v1/skus?category=pullover&limit=200 HTTP/1.1
Authorization: Bearer <token>
```

#### Example response

```json
{
  "skus": [
    {
      "sku": "STTU758_C318_M",
      "productId": "STTU758",
      "productName": "Creator",
      "category": "tshirt",
      "brand": "Stanley/Stella",
      "colorCode": "C318",
      "colorName": "Red",
      "colorGroup": "red",
      "hex": "#d2232a",
      "size": "M",
      "image": "https://img.dinschrift.ch/packshots/STTU758_C318_M.jpg",
      "price": {
        "unitPrice": 13.20,
        "currency": "CHF"
      }
    },
    {
      "sku": "STTU758_C318_L",
      "productId": "STTU758",
      "productName": "Creator",
      "category": "tshirt",
      "brand": "Stanley/Stella",
      "colorCode": "C318",
      "colorName": "Red",
      "colorGroup": "red",
      "hex": "#d2232a",
      "size": "L",
      "image": "https://img.dinschrift.ch/packshots/STTU758_C318_L.jpg",
      "price": {
        "unitPrice": 13.80,
        "currency": "CHF"
      }
    }
  ],
  "nextCursor": "eyJpZCI6ICJTVFRVNzU4X0MzMThfTCJ9"
}
```

#### Field meanings

All SKU fields have the same meaning as in `GET /v1/products/{productId}`, with additional context fields:

| Field         | Type   | Description                                           |
|---------------|--------|-------------------------------------------------------|
| `productId`   | string | Product code (e.g. `STTU758`).                        |
| `productName` | string | Product name (e.g. `Creator`).                        |
| `category`    | string | Normalised category (`tshirt`, `pullover`, etc.).     |
| `brand`       | string | Brand name.                                           |
| `nextCursor`  | string | Cursor for fetching the next page of results.        |

Clients can repeatedly call `GET /v1/skus` with the `cursor` value until no `nextCursor` is returned (end of results).

---

### 4.4. `GET /v1/skus/{sku}`

Returns details about a **single SKU**.

- **Auth:** required  
- **Path parameter:** `sku` – the SKU code (e.g. `STTU758_C001_M`).  
- **Response:** SKU details, similar to a single entry in `GET /v1/skus`.

Example response (conceptual):

```json
{
  "sku": "STTU758_C001_M",
  "productId": "STTU758",
  "productName": "Creator",
  "brand": "Stanley/Stella",
  "category": "tshirt",
  "colorCode": "C001",
  "colorName": "Off White",
  "colorGroup": "white",
  "hex": "#f7f3ef",
  "size": "M",
  "image": "https://img.dinschrift.ch/packshots/STTU758_C001_M.jpg",
  "price": {
    "unitPrice": 13.20,
    "currency": "CHF"
  },
  "allowedSides": ["front", "back"],
  "defaultProfile": "LGT"
}
```

---

## 5. Stock endpoints

These endpoints allow integrators to check stock before creating orders.

> **Status:** conceptual – not yet implemented in the pilot.

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

Submit a new order to Dinschrift using POD-JSON Lite.

> **Status:** designed – implementation in progress.  
> Pilot integrators should coordinate with Dinschrift before using this endpoint.

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
  "apiOrderId": "DINSCHR-1732715111225",
  "status": "ACCEPTED",
  "receivedAt": "2025-11-26T11:14:06.633Z"
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

Fetch details and status of a single order.

> **Status:** planned – not yet implemented in the pilot.

- **Auth:** required  
- **Path parameter:** `apiOrderId` – the order ID returned by `POST /v1/orders`.

- **Response body (example):**

```json
{
  "apiOrderId": "DINSCHR-98765",
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

### 6.4. `GET /v1/orders/{clientId}` (list orders)

Returns a list of orders associated with a specific partner identifier.  
In the current pilot implementation, this endpoint is used primarily for internal and partner-facing dashboards.

- **Auth:** required  
- **Path parameter:**
  - `clientId` (required) – partner-specific identifier (provided by Dinschrift).

Example request:

```http
GET /v1/orders/707a106d7bd71aa0a17e HTTP/1.1
Authorization: Bearer <token>
```

Example successful response (real-world sample, truncated):

```json
[
  {
    "OrderId": "101575614766532680",
    "Date": "22.10.2025",
    "OrderNr": 50116,
    "TotalQuantity": 1,
    "TotalAmount": "59.05",
    "Delivery": 1,
    "Status": 99,
    "URL": "/app/order?nr=101575614766532680"
  },
  {
    "OrderId": "101575614766532511",
    "Date": "22.10.2025",
    "OrderNr": 50114,
    "TotalQuantity": 1,
    "TotalAmount": "59.10",
    "Delivery": 0,
    "Status": 99,
    "URL": "/app/order?nr=101575614766532511"
  }
]
```

#### Field meanings

- **OrderId** – Internal Dinschrift order reference (long string ID).  
- **Date** – Order date in `DD.MM.YYYY` format.  
- **OrderNr** – Internal numeric order number.  
- **TotalQuantity** – Sum of all items in the order.  
- **TotalAmount** – Total order value in CHF.  
- **Delivery** – Delivery mode flag (0/1/2 – internal codes).  
- **Status** – Internal status code (`99` = completed/archived in the current system).  
- **URL** – Internal backoffice link (mainly for Dinschrift’s own UI; may be useful for deep links in some integrations).

> **Note:** The exact shape of this response is influenced by the existing Dinschrift backend. It may be normalised further in future versions (e.g. ISO 8601 dates, structured status and delivery enums).

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

In addition to polling `GET /v1/orders/{apiOrderId}` or `GET /v1/orders/{clientId}`, the API may support **webhooks** in the future:

- Integrator provides a `callbackUrl` at account level or per order.
- Dinschrift sends HTTP `POST` requests to that URL on significant events:
  - order accepted
  - status changed to `IN_PRODUCTION`, `SHIPPED`, etc.

Webhook design is outside the scope of this initial concept but can be added in a future version of this document.
