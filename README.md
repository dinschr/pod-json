# POD-JSON Specification

POD-JSON is an open JSON format for describing **print-on-demand (POD) products**, starting with apparel.

It is designed to be:

- **AI-friendly** – easy to generate from natural language prompts
- **Production-ready** – carries everything a print workflow / RIP needs
- **Platform-neutral** – can be embedded into Shopify orders or any other system
- **Extensible** – allows vendor-specific fields via `extensions`

> Status: **Draft – internal work-in-progress**

---

## Goals

1. Provide a stable JSON structure for T-shirt and apparel printing.
2. Make it easy for AI systems to convert text prompts into POD orders.
3. Standardize how print areas, design layers, and production settings are described.
4. Be simple enough to adopt in commerce platforms (e.g. Shopify, custom carts).

---

## Repository Structure

Current layout:

- `spec/v1.0/pod-json-v1.0.md`  
  Human-readable **POD-JSON v1.0** specification.

- `schemas/pod-order.v1.json`  
  JSON Schema for validating a `PodOrder` document.

- `examples/`  
  Example POD-JSON payloads:
  - `simple-tshirt.json` – minimal single-side T-shirt example
  - (more examples will be added over time)

- `docs/` (optional, internal)  
  Draft content for future documentation / GitHub Pages site.

---

## Core Concepts

POD-JSON v1.0 centers around a few key objects:

- **`PodOrder`** – top-level object representing one order.
- **`PodLineItem`** – one product in that order (e.g. one T-shirt).
- **`PodSide`** – a printable side (front, back, sleeves).
- **`PodDesignLayer`** – one design element on a side (image / SVG / text).

The JSON Schema in `schemas/pod-order.v1.json` defines these structures formally.

For the full description of all fields, see:

- [`spec/v1.0/pod-json-v1.0.md`](spec/v1.0/pod-json-v1.0.md)

---

## Using POD-JSON

### 1. Validation

You can validate POD-JSON documents against the schema in `schemas/pod-order.v1.json` using any JSON Schema validator (Node, Python, PHP, etc.).

Example (Node, pseudo-code):

```js
// load pod-order.v1.json as your JSON Schema
// validate incoming POD-JSON payloads before processing
