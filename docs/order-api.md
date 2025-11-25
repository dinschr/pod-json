# Order API Concept (POD-JSON Lite)

This document describes a **conceptual** HTTP API that uses POD-JSON Lite as its order payload.

The purpose is to define a simple, predictable way for an integrator’s system to:

- discover products and SKUs,
- check stock,
- submit orders,
- read order statuses.

> **Note:** This document is conceptual and may not match any live implementation 1:1.  
> Exact URLs and behaviour will be agreed with integrators during onboarding.

---

## 1. Authentication

All endpoints (except possibly `/health`) require authentication using a **Bearer token**.

Each integration partner receives one or more tokens from Dinschrift.

### 1.1. Request header

```http
Authorization: Bearer <token>
