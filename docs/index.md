# POD-JSON – Overview

POD-JSON defines how one machine can ask another machine to **print and ship customised textile products**.  
It is a minimal, practical specification created for real B2B print-on-demand workflows.

The goals are:

- **Simplicity** – easy to implement and validate.  
- **Predictability** – clear, well-defined JSON structure.  
- **Workflow alignment** – mirrors Dinschrift’s actual production process.

This repository contains:

1. **POD-JSON Lite** – the order data model.  
2. The **Order API concept** – a REST-style HTTP interface that uses POD-JSON Lite.

---

## 1. Design goals

### **Workflow first**
Designed around Dinschrift’s real POD workflow:

- SKU‑based products  
- Known print areas  
- RIP-friendly profiles (`LGT`, `DRK`, `WHTP`, `NOTRANS`)  
- Automation‑ready file handling  

### **Minimal but complete**
Only the fields required to:

- determine **what** to print  
- determine **how** to print  
- determine **where** to ship  
- determine **how many** items  

Everything else is handled internally (DPI, design placement, RIP config).

### **Easy for integrators**
A partner should be able to:

1. Pick a SKU  
2. Provide a design URL or ID  
3. Select a print profile  
4. Submit **one JSON**  

### **Stable and evolvable**
Every order includes a `schemaVersion` so future versions remain backward compatible.

👉 Format: [`pod-json-lite.md`](./pod-json-lite.md)

---

## 2. POD-JSON Lite

POD-JSON Lite is the **single JSON object** used to submit a print-on-demand textile order.

Each order contains:

- Order metadata  
- A shipping address  
- One or more **OrderLines**  
- Each line includes:
  - a SKU  
  - quantity  
  - a **PrintSpec**  
    - print side  
    - print profile  
    - design source (URL or ID)  

This is the core payload consumed by the Order API.

---

## 3. Order API (concept)

A simple, modern **REST-style API** for machines that need to place or track textile print orders.

Core endpoints include:

- `GET /v1/health` – API availability  
- `GET /v1/products` – product models  
- `GET /v1/skus` – browse/filter SKUs  
- `GET /v1/skus/{sku}` – SKU detail  
- `GET /v1/stock` – stock for a SKU  
- `POST /v1/stock/query` – batch stock query  
- `POST /v1/orders` – submit an order  
- `GET /v1/orders/{id}` – order details/status  

Authentication uses **Bearer tokens**.  
Billing and payment are handled separately (invoice or prepaid credit model).

👉 Full API concept: [`order-api.md`](./order-api.md)

---

## 4. Status & usage

> **Pilot / Draft Specification**  
> Changes may occur as integrators begin testing.

To integrate:

1. Contact Dinschrift  
2. Obtain API credentials  
3. Review POD‑JSON Lite  
4. Test the API in staging  
5. Move to production with approval  

---

## 5. Repository contents

- [`pod-json-lite.md`](./pod-json-lite.md) – POD-JSON Lite format  
- [`order-api.md`](./order-api.md) – Order API concept  
- `README.md` – project overview  
- `index.md` – this page

