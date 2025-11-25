# POD-JSON – Print-on-Demand JSON & Order API

POD-JSON is a simple JSON format and HTTP API concept for sending print-on-demand T-shirt orders from one machine to another.

This repository describes:

- **POD-JSON Lite** – a minimal order data model that fits Dinschrift’s real production workflow.
- The **Order API** concept – how external systems can submit orders, check stock, and read order status.

The focus is on:

- Keeping the format **simple** for integrators.
- Matching **Dinschrift’s internal workflow** (SKU-based, RIP-driven production).
- Being easy to evolve as we learn.

> **Status:**  
> This specification is in **pilot / draft** state and may change. Do not rely on it in production without a direct agreement with Dinschrift.

---

## Documentation

- **Overview:**  
  See [docs/index.md](docs/index.md)

- **POD-JSON Lite format:**  
  See [docs/pod-json-lite.md](docs/pod-json-lite.md)

- **Order API concept (HTTP):**  
  See [docs/order-api.md](docs/order-api.md)

---

## Scope

This repo contains:

- The data model for **POD-JSON Lite orders**.
- A conceptual design for a **machine-to-machine order API**:
  - submit orders
  - check stock
  - fetch basic product/SKU info
  - read order status

It does **not** contain:

- Backend source code
- API keys or secrets
- Billing or payment processing implementation

---

## License

Unless otherwise stated in individual files, the written specification in this repository is made available under the **MIT License**.

You are free to read, implement, and experiment with this spec. Commercial use with Dinschrift as the print provider requires a separate commercial agreement.
