# POD-JSON Specification

POD-JSON is an open JSON format for describing **print-on-demand (POD) products**, starting with apparel.

It is designed to be:

- **AI-friendly** – easy to generate from natural language prompts
- **Production-ready** – carries everything a print workflow / RIP needs
- **Platform-neutral** – can be embedded into Shopify orders or any other system
- **Extensible** – allows vendor-specific fields via `extensions`

> Status: **Draft – internal work-in-progress**

## Goals

1. Provide a stable JSON structure for T-shirt and apparel printing.
2. Make it easy for AI systems to convert text prompts into POD orders.
3. Standardize how print areas, design layers, and production settings are described.
4. Be simple enough to adopt in commerce platforms (e.g. Shopify, custom carts).

## Repository Structure

- `spec/v1.0/pod-json-v1.0.md` — Human-readable POD-JSON v1.0 specification.
- `schemas/pod-order.v1.json` — JSON Schema for validating a PodOrder.
- `examples/` — Example POD-JSON payloads.
- `docs/` — Internal future documentation drafts.

## Core Concepts

- **PodOrder** — top-level object describing one order.
- **PodLineItem** — one product.
- **PodSide** — a printable side.
- **PodDesignLayer** — an element on the print area.

See `spec/v1.0/pod-json-v1.0.md` for full details.

## Versioning

Semantic versioning: `MAJOR.MINOR.PATCH`.

Current: **1.0.0 (draft)**  
`pod_json_version` must be `"1.0.0"`.

## Contribution

Currently private. Contributions handled directly.

## License

To be decided (MIT / Apache 2.0 candidate).

## Authors

Developed by **Dinschrift GmbH** / **Rich Green**.
