# POD-JSON Documentation

Welcome to the official documentation hub for **POD-JSON**, an open JSON format for describing print‑on‑demand (POD) products.

POD-JSON provides a consistent, platform‑neutral way to describe:

- Products (e.g. T‑shirts, hoodies, totes)
- Print areas (front, back, sleeves)
- Design layers (images, SVG, text)
- Production settings
- Everything needed for a complete print job

This documentation will grow as the specification evolves.

---

## 📘 Specification

The current version of the specification is:

**POD-JSON v1.0 (draft)**  
→ [View the full spec](../spec/v1.0/pod-json-v1.0.md)

This document defines:

- PodOrder  
- PodLineItem  
- PodSide  
- PodDesignLayer  
- Production metadata  
- Required and optional fields  
- Examples  

---

## 🧩 JSON Schema

The schema provides a strict machine‑readable definition:

- **POD‑JSON Order Schema**  
  → [`schemas/pod-order.v1.json`](../schemas/pod-order.v1.json)

Use this to validate incoming POD‑JSON payloads in your API, webshop, or print pipeline.

---

## 📦 Examples

Example POD‑JSON documents:

- **Simple T-shirt**  
  → [`examples/simple-tshirt.json`](../examples/simple-tshirt.json)

More examples (hoodie, tote bag, multi‑side items) will be added.

---

## 🛣️ Roadmap

Future releases and feature plans are tracked here:

→ [`ROADMAP.md`](../ROADMAP.md)

---

## 🧭 Project Goals

POD‑JSON aims to:

- Make AI‑generated POD orders reliable and structured  
- Standardize product/print metadata across systems  
- Enable easy integration between design tools, AI models, ecommerce, and production  
- Provide a common language for POD workflows  

---

## 📜 Versioning

POD‑JSON follows **semantic versioning**:

- `1.0.x` – patches  
- `1.x.0` – backwards‑compatible additions  
- `2.0.0` – breaking changes  

Current version: **1.0.0 (draft)**

---

## 🧪 Status

The project is currently under active development in a private repository.  
Public release and GitHub Pages hosting may follow once the spec stabilizes.

---

## 👤 Authors

POD‑JSON is developed and maintained by:

**Dinschrift GmbH**  
**Rich Green**

If this project becomes public, contributors will be listed here.

---

## 📂 Repository Structure

```
pod-json-spec/
 ├── spec/
 │    └── v1.0/
 │         └── pod-json-v1.0.md
 ├── schemas/
 │    └── pod-order.v1.json
 ├── examples/
 │    └── simple-tshirt.json
 ├── docs/
 │    └── index.md
 ├── ROADMAP.md
 └── README.md
```

---

## 💬 Feedback

This documentation is a starting point and will expand as the specification grows.  
If you'd like to add more sections or restructure the docs, feel free to request it.
