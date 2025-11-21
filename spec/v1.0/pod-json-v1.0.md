# POD-JSON v1.0 Specification

**POD-JSON** is a JSON format for describing **print-on-demand (POD) products**, starting with apparel.

It is designed to be:

- **AI-friendly** – easy to generate from natural language prompts.
- **Production-ready** – contains enough detail for RIP and print workflows.
- **Platform-neutral** – can be embedded inside ecommerce systems (e.g. Shopify orders).
- **Extensible** – allows vendor-specific data via `extensions`.

This document defines **POD-JSON v1.0.0**.

---

## 1. Versioning

The top-level field:

```json
"pod_json_version": "1.0.0"
