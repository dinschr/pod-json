# POD-JSON v1.0 Specification

**POD-JSON** is a JSON format for describing print-on-demand products (especially apparel) in a way that:

- Is easy for AI systems to generate from natural language
- Is stable enough for production workflows (RIP, DTG, DTF, screen, etc.)
- Can be embedded inside ecommerce platforms (e.g. Shopify orders)

## 1. Version

- `pod_json_version`: `"1.0.0"`

## 2. Top-level: `PodOrder`

A `PodOrder` represents one logical order that contains one or more line items.

```jsonc
{
  "pod_json_version": "1.0.0",
  "order_id": "WEB-2025-000123",
  "external_order_ref": "shopify:123456789",
  "currency": "CHF",
  "source": { "...": "..." },
  "customer": { "...": "..." },
  "shipping_address": { "...": "..." },
  "line_items": [ { /* PodLineItem */ } ],
  "production": { "...": "..." },
  "totals": { "...": "..." },
  "meta": { "...": "..." }
}
