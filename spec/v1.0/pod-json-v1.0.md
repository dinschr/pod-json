> ⚠️ **POD-JSON v1.0 Draft**  
> This is a draft version of the POD-JSON specification.  
> All content is subject to revision.
>
> # POD-JSON v1.0 Specification

POD-JSON is an open JSON format for describing print-on-demand (POD) products.

## 1. Versioning

The top-level field:

```json
{
  "pod_json_version": "1.0.0"
}
```

- Required
- Uses semantic versioning (MAJOR.MINOR.PATCH)
- This document defines version 1.0.0

## 2. Top-Level Structure: PodOrder

A PodOrder represents one complete print order.

### Example

```json
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
  "line_items": [],
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
```

## 3. PodLineItem

Describes a product and print configuration.

### Example

```json
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
  "sides": {},
  "extensions": {}
}
```

## 4. PodSide

### Example

```json
{
  "enabled": true,
  "print_area": {
    "print_area_id": "front",
    "label": "Front Center",
    "canvas_size": {
      "width_mm": 320,
      "height_mm": 420,
      "dpi": 300
    },
    "origin": "top_center",
    "coordinate_space": "mm"
  },
  "design_layers": [],
  "render_preview": {
    "packshot_url": "https://example.com/pack.png",
    "preview_url": "https://example.com/preview.png"
  },
  "production_settings": {
    "print_method": "dtg",
    "white_underbase": true,
    "color_profile": "sRGB",
    "max_rip_resolution_dpi": 600
  }
}
```

## 5. PodDesignLayer

### Example

```json
{
  "layer_id": "L1",
  "type": "image",
  "source": {
    "url": "https://example.com/design.png",
    "mime_type": "image/png",
    "checksum_sha256": null
  },
  "position": {
    "x_mm": 0,
    "y_mm": 40,
    "anchor": "top_center"
  },
  "size": {
    "width_mm": 260,
    "height_mm": 200,
    "lock_aspect_ratio": true
  },
  "rotation_deg": 0,
  "opacity": 1.0,
  "z_index": 0,
  "text": null,
  "effects": {
    "knockout_background": true,
    "distress_profile": null,
    "blend_mode": "normal"
  }
}
```

## 6. Minimal AI Example

```json
{
  "pod_json_version": "1.0.0",
  "order_id": "AI-ORDER-0001",
  "currency": "CHF",
  "source": {
    "channel": "ai-agent",
    "app_name": "Prompt2Print",
    "app_version": "1.0.0"
  },
  "customer": null,
  "shipping_address": null,
  "line_items": [],
  "production": {
    "priority": "standard",
    "due_date": null,
    "print_provider": "internal",
    "notes": null
  },
  "totals": null,
  "meta": {
    "created_at": "2025-11-21T10:20:00Z"
  }
}
```

## 7. JSON Schema

See: schemas/pod-order.v1.json

## 8. Status

Version 1.0.0 (draft)
