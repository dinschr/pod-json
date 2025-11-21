# POD-JSON v1.0 Specification

POD-JSON is an open JSON format for describing print-on-demand (POD) products, especially apparel.

It is designed to be:

- AI-friendly – easy to generate from natural language prompts  
- Production-ready – contains enough detail for RIP and print workflows  
- Platform-neutral – can be embedded inside ecommerce systems (e.g. Shopify Orders)  
- Extensible – allows vendor-specific data via “extensions”  

This document defines POD-JSON version 1.0.0.

-------------------------------------------------------
1. Versioning
-------------------------------------------------------

The top-level field:

"pod_json_version": "1.0.0"

• Is required  
• Uses semantic versioning (MAJOR.MINOR.PATCH)  
• This document defines version 1.0.0  

Future versions:

• PATCH: backwards compatible (1.0.x)  
• MINOR: backwards compatible additions (1.x.0)  
• MAJOR: breaking changes (2.0.0)

-------------------------------------------------------
2. Top-Level Structure: PodOrder
-------------------------------------------------------

A PodOrder represents one complete print order containing one or more items.

Example:

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

Field descriptions:

pod_json_version (string, required)  
Version of the POD-JSON spec.

order_id (string|null)  
Internal ID for the order.

external_order_ref (string|null)  
Reference to external system, e.g. Shopify.

currency (string|null)  
ISO 4217 code: CHF, EUR, USD.

source (object|null):  
• channel  
• shop_domain  
• app_name  
• app_version  

customer (object|null):  
• customer_id  
• email  
• first_name  
• last_name  

shipping_address (object|null):  
• name  
• company  
• street_1  
• street_2  
• postal_code  
• city  
• country_code  

line_items (array, required)  
One or more PodLineItem entries.

production (object|null):  
• priority (“standard” | “express”)  
• due_date (ISO datetime)  
• print_provider  
• notes  

totals (object|null):  
• subtotal  
• shipping  
• tax  
• discount  
• grand_total  

meta (object|null):  
• created_at  
• updated_at  

-------------------------------------------------------
3. PodLineItem
-------------------------------------------------------

Each PodLineItem describes one product + print configuration.

Example:

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

Fields:

line_item_id (string, required)  
Unique inside the order.

sku (string|null)

product_ref (object|null):  
• catalog_id  
• variant_id  
• barcode  

name (string|null)

color_name (string|null)  
color_code (string|null)  
size (string|null)

quantity (integer, required)  

unit_price (string|null)  
line_total (string|null)

print_profile (string|null)  
Used for RIP mapping, e.g. DTG_LIGHT, DTG_DARK.

sides (object, required):  
• front  
• back  
• left_sleeve  
• right_sleeve  
Each is PodSide or null.

extensions (object)  
Vendor-specific fields.

-------------------------------------------------------
4. PodSide
-------------------------------------------------------

Describes a printable side of a garment.

Example:

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

Fields:

enabled (boolean, required)

print_area (object, required):  
• print_area_id  
• label  
• canvas_size (width_mm, height_mm, dpi)  
• origin  
• coordinate_space (“mm” or “px”)

design_layers (array)

render_preview (object|null):  
• packshot_url  
• preview_url

production_settings (object|null):  
• print_method  
• white_underbase  
• color_profile  
• max_rip_resolution_dpi  

-------------------------------------------------------
5. PodDesignLayer
-------------------------------------------------------

Each layer represents one element on the print.

Example:

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

Fields:

layer_id (string)  
type (“image”, “svg”, “text”)

source (object|null):  
• url  
• mime_type  
• checksum_sha256  

position:  
• x_mm  
• y_mm  
• anchor  

size:  
• width_mm  
• height_mm  
• lock_aspect_ratio  

rotation_deg  
opacity  
z_index  

text (object|null for text layers):  
• content  
• font_family  
• font_size_pt  
• fill_color  
• stroke_color  
• stroke_width_pt  
• text_align  
• line_height  
• letter_spacing  

effects (object|null):  
• knockout_background  
• distress_profile  
• blend_mode  

-------------------------------------------------------
6. Minimal AI-friendly Example
-------------------------------------------------------

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

-------------------------------------------------------
7. JSON Schema
-------------------------------------------------------

The authoritative schema for POD-JSON v1.0 is located at:

schemas/pod-order.v1.json

-------------------------------------------------------
8. Status
-------------------------------------------------------

Version: 1.0.0 (draft)  
Scope: Apparel  
Future expansions: more product types, more print methods.
