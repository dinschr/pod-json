# POD-JSON – Overview

POD-JSON defines how one machine can ask another machine to print and ship custom T-shirts.

The goal is **simplicity**:

- Simple for integrators to understand and implement.
- Simple for Dinschrift to map into its real production workflow.
- Focused on **orders**, not on every possible POD scenario.

This repository defines:

1. **POD-JSON Lite** – a minimal JSON order format.
2. A **machine-to-machine Order API** concept that uses POD-JSON Lite.

---

## 1. Design goals

- **Workflow first**  
  The format is designed around Dinschrift’s actual workflow:  
  SKU-based products, fixed print areas, RIP-friendly profiles and naming.

- **Minimal JSON**  
  Only the information that is needed to:
  - know what to print,
  - where to ship it, and
  - how many items to produce.

- **Easy for integrators**  
  An integrator should be able to:
  - pick a SKU,
  - supply a design file URL or design ID,
  - choose a print profile (e.g. `LGT` or `DRK`),
  - send one order JSON.

- **Stable but evolvable**  
  The format carries a `schemaVersion` so future versions can extend it without breaking current users.

---

## 2. POD-JSON Lite

POD-JSON Lite is the core order object that integrators send to the Dinschrift Order API.

Key ideas:

- One **Order** contains:
  - Order metadata (IDs, currency, shipping method)
  - Shipping address
  - One or more **OrderLines**

- Each **OrderLine** contains:
  - A SKU
  - A quantity
  - A **PrintSpec**

- The **PrintSpec** tells Dinschrift:
  - Which side to print
  - Which print profile to use (e.g. `LGT`, `DRK`, `WHTP`)
  - Where to get the design:
    - either a `designUrl`
    - or a `designId` already known to Dinschrift

Print areas, DPI, coordinates, RIP configuration etc. are handled internally by Dinschrift based on SKU, side and profile.

Full details:  
➡️ [POD-JSON Lite format](./pod-json-lite.md)

---

## 3. Order API (concept)

The Order API is a simple HTTP JSON API that uses POD-JSON Lite as its order payload.

Planned core endpoints:

- `GET /v1/health` – basic health check
- `GET /v1/products` – list available products
- `GET /v1/skus/{sku}` – detailed info for a single SKU
- `GET /v1/stock` – check stock for a specific SKU
- `POST /v1/stock/query` – batch stock query
- `POST /v1/orders` – submit an order (POD-JSON Lite)
- `GET /v1/orders/{id}` – read order status and details

Authentication is done with **Bearer tokens** issued by Dinschrift for each integration partner.

Payments and billing are treated as a **separate concern** (e.g. invoice or prepaid credit). The API itself only needs to know whether an account is allowed to place orders.

Full details:  
➡️ [Order API concept](./order-api.md)

---

## 4. Status & usage

> **Pilot / draft**  
> This specification is in an experimental phase and may change based on feedback and internal learning.

If you are interested in integrating with Dinschrift using this spec:

- Get in touch with Dinschrift to discuss:
  - commercial terms,
  - access to a test environment,
  - API key issuance.

Implementation of this spec without a direct agreement does not automatically give access to Dinschrift’s production systems.
