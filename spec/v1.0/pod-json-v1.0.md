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

{
  "pod_json_version": "1.0.0",
  "order_id": "WEB-2025-000123",
  "external_order_ref": "shopify:123456789",
  "currency": "CHF",
  "source": {
    "channel": "shopify",
    "shop_domain": "dinschrift.ch",
    "app_name": "Dinschrift Designer",
    "app_version": "2.2.0"
  },
  "customer": {
    "customer_id": "12345",
    "email": "customer@example.com",
    "first_name": "Alex",
    "last_name": "Muster"
  },
  "shipping_address": {
    "name": "Alex Muster",
    "company": "Muster GmbH",
    "street_1": "Musterstrasse 10",
    "street_2": null,
    "postal_code": "8000",
    "city": "Zürich",
    "country_code": "CH"
  },
  "line_items": [
    { }
  ],
  "production": {
    "priority": "express",
    "due_date": "2025-11-24T00:00:00Z",
    "print_provider": "internal",
    "notes": "Please ship in plain packaging."
  },
  "totals": {
    "subtotal": "24.90",
    "shipping": "9.00",
    "tax": "2.90",
    "discount": "0.00",
    "grand_total": "36.80"
  },
  "meta": {
    "created_at": "2025-11-21T10:15:00Z",
    "updated_at": "2025-11-21T10:16:00Z"
  }
}

{
  "line_item_id": "item-1",
  "sku": "STTU758-C504-L",
  "product_ref": {
    "catalog_id": "STTU758",
    "variant_id": "STTU758-C504-L",
    "barcode": "1234567890123"
  },
  "name": "Stanley Stella Creator T-Shirt",
  "color_name": "Vintage White",
  "color_code": "C504",
  "size": "L",
  "quantity": 1,
  "unit_price": "24.90",
  "line_total": "24.90",
  "print_profile": "DTG_LIGHT",
  "sides": {
    "front": { },
    "back": null,
    "left_sleeve": null,
    "right_sleeve": null
  },
  "extensions": {
    "dinschrift": {
      "article_id": 1234,
      "design_v1_id": "abcde-12345"
    }
  }
}
