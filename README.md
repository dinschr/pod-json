# POD-JSON – Dinschrift Print-on-Demand REST API

POD-JSON is a simple JSON format and REST-style HTTP API concept for sending print-on-demand T-shirt and textile orders **from one machine to another**.

This repository describes:

- **POD-JSON Lite** – a minimal order data model that fits Dinschrift’s real production workflow.
- The **Order API** concept – how external systems can:
  - submit orders,
  - check stock,
  - fetch product / SKU information,
  - read order status.

The focus is on:

- Keeping the format **simple** for integrators (including low-code tools).
- Matching **Dinschrift’s internal workflow** (SKU-based, RIP-driven production).
- Being easy to evolve as we learn from pilots.

> **Status**  
> This specification is in **pilot / draft** state and may change.  
> Do not rely on it in production without a direct agreement with Dinschrift.

---

## Who is this for?

This repository is aimed at:

- Integrators who want to send **direct B2B POD orders** to Dinschrift.
- Developers building:
  - Shopify / shop integrations,
  - internal tools,
  - automation (Zapier, Make, n8n, etc.),
  that need a clear, stable JSON format for T-shirt and textile orders.

It is **not** a general-purpose public POD standard (yet), but it is designed to be understandable and reusable outside Dinschrift.

---

## Documentation

- **Overview**  
  High-level introduction to POD-JSON Lite and the API concept:  
  [`docs/index.md`](docs/index.md)

- **POD-JSON Lite format**  
  The order JSON structure used by the API (addresses, lines, print profiles, etc.):  
  [`docs/pod-json-lite.md`](docs/pod-json-lite.md)

- **Order API concept (HTTP)**  
  Endpoints, authentication, error model, products/SKUs, stock and orders:  
  [`docs/order-api.md`](docs/order-api.md)

---

## Scope

This repo contains:

- The data model for **POD-JSON Lite orders**.
- A conceptual design for a **machine-to-machine order API**:
  - submit orders,
  - check stock,
  - fetch product / SKU information,
  - read order status.

This repo does **not** contain:

- Backend source code or deployment scripts,
- API keys, secrets or live credentials,
- Billing or payment processing implementation,
- Service-level commitments (SLAs).

Commercial use with Dinschrift as the print provider always requires a separate commercial agreement.

---

## Stability and changes

Because this spec is in **pilot**, we may:

- add new fields (in a backwards-compatible way),
- clarify behaviour and error codes,
- add new endpoints (e.g. webhooks, account balance).

Breaking changes, if ever needed, will be introduced via **versioning** (for example a future `pod-json-lite-0.2` or `/v2/` API namespace) and documented in this repo.

---

## License

Unless otherwise stated in individual files, the written specification in this repository is made available under the **MIT License**.

You are free to:

- read, implement and experiment with this spec,
- use it internally or with Dinschrift under a commercial agreement.

If you build something based on POD-JSON, we are always happy to hear about it.
