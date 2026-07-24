---
name: Cash on-ramp with a barcode order template
description: Find a nearby cash location and generate a barcode order template so a user can buy crypto with cash in store.
api: openapi/coinme-caas-openapi.json
operations: [initiate-authorize, get-cash-ramp-locations, generate-order-template, get-order-template]
---

# Cash on-ramp with a barcode order template (Coinme CaaS)

## Auth
1. `initiate-authorize` — obtain and cache the JWT; send `Authorization` bearer + `x-api-key`.

## Steps
2. `get-cash-ramp-locations` — POST `/locations/v2/query` with coordinates to list physical cash on-ramp stores sorted by distance.
3. `generate-order-template` — POST `/order-template` to create an order template (barcode) the user presents in store to fund a cash purchase.
4. `get-order-template` — GET `/order-template/templates/{customerId}` to list the customer's order templates and track status.

## Rules
- An order template can only be invalidated (`delete-order-template`) while in the `CREATED` status.
- Test the barcode scan flow in staging first (docs.coinme.com/docs/testing-barcode-scan-flow-in-staging).
- Subscribe to `CAAS` / `ORDER_TRACKING` webhook notifications for order status updates.
