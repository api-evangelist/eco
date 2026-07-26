---
name: Track cross-chain intents
description: Look up Eco Routes intents by hash or address and find refundable intents.
api: openapi/eco-routes-openapi-original.json
operations:
  - IntentsController_getIntentByHash
  - IntentsController_getIntentsByAddress
  - IntentsController_getRefundableIntents
  - IntentsController_getIntentStatusArray
base_url: https://quotes.eco.com
auth: none
---

# Track cross-chain intents (Eco Routes)

Inspect the status and history of Eco Routes intents. All calls are HTTP `POST`
with a JSON body; no authentication is required.

## Steps

1. **Look up one intent by hash** — `POST /api/v3/intents/intentByHash`
   (`IntentsController_getIntentByHash`). Pass the intent hash to retrieve its
   full record (route, reward, transactions, current status).

2. **List intents for an address** — `POST /api/v3/intents/intentsByAddress`
   (`IntentsController_getIntentsByAddress`). Provide an address and filter
   criteria in the body to enumerate that address's intents.

3. **Batch status check** — `POST /api/v3/intents/status`
   (`IntentsController_getIntentStatusArray`). Submit multiple hashes to get their
   statuses together. A `404` ("No intents found") means nothing matched the
   criteria.

4. **Find refundable intents** — `POST /api/v3/intents/refundableIntents`
   (`IntentsController_getRefundableIntents`). Identify intents that were not
   fulfilled and are eligible for refund from their per-intent vault.

## Notes
- Responses use the `EcoAPIResponse` envelope; see `errors/eco-problem-types.yml`.
- Entity relationships (Intent -> Route/Reward/Transaction) are documented in
  `data-model/eco-data-model.yml`.
