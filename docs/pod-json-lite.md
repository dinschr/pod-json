# POD-JSON Lite Specification

POD-JSON Lite is a minimal JSON format for sending print-on-demand T-shirt orders to Dinschrift.

It is designed to:

- match Dinschrift’s internal workflow (SKU-based production),
- be easy for integrators to understand,
- carry only the information needed to print and ship.

Current version: **`pod-json-lite-0.1`**

---

## 1. Top-level Order

An Order is the root object submitted to the Order API.

### 1.1. Fields

- `schemaVersion` (string, required)  
  Must be `pod-json-lite-0.1` for this version.

- `orderId` (string, required)  
  The integrator’s primary order reference (e.g. shop order number).  
  Must be unique per integrator.

- `externalRef` (string, optional)  
  Secondary reference (e.g. cart ID, marketplace ID).

- `currency` (string, required)  
  ISO 4217 currency code, e.g. `"CHF"`, `"EUR"`.

- `shippingMethod` (string, required)  
  Code agreed between the integrator and Dinschrift, e.g. `"POST_PRIORITY"`.

- `shippingAddress` (object, required)  
  A [Shipping Address](#3-shipping-address) object.

- `lines` (array of objects, required)  
  List of [OrderLines](#2-orderline).

---

## 2. OrderLine

Each OrderLine represents a single SKU and quantity, with associated print information.

### 2.1. Fields

- `lineId` (string, required)  
  Line identifier, unique within the order (e.g. `"1"`, `"A1"`).

- `sku` (string, required)  
  The exact SKU code Dinschrift recognises (product + colour + size).  
  Example: `"STTU758_C001_M"`.

- `quantity` (integer, required)  
  Number of units to produce. Must be `>= 1`.

- `print` (object, required)  
  A [PrintSpec](#4-printspec) object.

- `lineNote` (string, optional)  
  Free text note for debugging or operator hints. Not guaranteed to be processed automatically.

---

## 3. Shipping Address

The shipping address describes where the parcel should be delivered.

### 3.1. Fields

- `firstName` (string, required)  
- `lastName` (string, required)  
- `company` (string, optional)  
- `street1` (string, required)  
- `street2` (string, optional)  
- `postalCode` (string, required)  
- `city` (string, required)  
- `countryCode` (string, required)  
  ISO 3166-1 alpha-2 country code, e.g. `"CH"`.

- `phone` (string, optional but recommended)  
  For carriers that require a phone number.

- `email` (string, optional but recommended)  
  For shipping notifications where supported.

---

## 4. PrintSpec

The PrintSpec describes *how* to print the design on the chosen SKU.

### 4.1. Fields

- `side` (string, required)  
  Where to print. Allowed values (initial set):

  - `"front"`
  - `"back"`
  - `"leftSleeve"`
  - `"rightSleeve"`
  - `"other"`

  The exact meaning and available options depend on the SKU and the commercial agreement.

- `profile` (string, required)  
  Print profile code understood by Dinschrift. Examples:

  - `"LGT"` – profile for light garments
  - `"DRK"` – profile for dark garments
  - `"WHTP"` – white print / special profile
  - `"NOTRANS"` – specific internal use profile

  Exact codes and usage are documented per integrator agreement.

- **Exactly one** of the following design reference fields:

  - `designUrl` (string, optional)  
    HTTPS URL pointing to the artwork file (e.g. PNG, SVG, PDF).  
    The integrator is responsible for ensuring:
    - the URL is reachable by Dinschrift’s backend,
    - the file format is supported as per agreement.

  - `designId` (string, optional)  
    An identifier for a design that Dinschrift already knows (for example from a previous upload or internal configuration).

- `notes` (string, optional)  
  Optional instructions for operators. Not guaranteed to be processed automatically.

### 4.2. Design reference rules

At least one of `designUrl` or `designId` **must** be present.  
If both are present, `designUrl` SHOULD be treated as primary unless otherwise agreed.

---

## 5. Example Order

The following is a complete example of a POD-JSON Lite order:

```json
{
  "schemaVersion": "pod-json-lite-0.1",
  "orderId": "PARTNER-12345",
  "externalRef": "SHOP-CART-789",
  "currency": "CHF",
  "shippingMethod": "POST_PRIORITY",
  "shippingAddress": {
    "firstName": "Anna",
    "lastName": "Muster",
    "company": "Muster GmbH",
    "street1": "Beispielstrasse 1",
    "street2": "",
    "postalCode": "8000",
    "city": "Zuerich",
    "countryCode": "CH",
    "phone": "+41441234567",
    "email": "anna@example.com"
  },
  "lines": [
    {
      "lineId": "1",
      "sku": "STTU758_C001_M",
      "quantity": 2,
      "print": {
        "side": "front",
        "profile": "LGT",
        "designUrl": "https://cdn.partner.com/files/design-1234.png"
      }
    },
    {
      "lineId": "2",
      "sku": "STTU758_C001_L",
      "quantity": 1,
      "print": {
        "side": "back",
        "profile": "DRK",
        "designId": "PARTNER-DESIGN-5678",
        "notes": "Print 5cm below collar."
      }
    }
  ]
}
