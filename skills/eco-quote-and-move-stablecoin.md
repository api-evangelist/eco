---
name: Quote and move a stablecoin cross-chain
description: Get a cross-chain stablecoin quote from Eco Routes and initiate a gasless intent, then track it to fulfillment.
api: openapi/eco-routes-openapi-original.json
operations:
  - QuotesV3Controller_createQuote
  - QuotesV3Controller_initiateGaslessIntent
  - IntentsController_getIntentStatus
base_url: https://quotes.eco.com
auth: none
---

# Quote and move a stablecoin cross-chain (Eco Routes)

Use the Eco Routes API to price and execute a cross-chain stablecoin transfer/swap.
All calls are HTTP `POST` with a JSON body. **No authentication** is required;
include a `dAppID` in the body for attribution.

## Conventions to respect
- API is versioned by URI path; use **V3** (`/api/v3/...`) for new work.
- Errors come back in the `EcoAPIResponse` envelope (not RFC 9457). See
  `errors/eco-problem-types.yml`.
- There is no `Idempotency-Key` header — idempotency is enforced onchain via the
  deterministic intent hash. See `conventions/eco-conventions.yml`.

## Steps

1. **Get the best quote** — `POST /api/v3/quotes/single`
   (`QuotesV3Controller_createQuote`). Provide the source/destination chains,
   tokens, and amount. Read back the quoted route and fees. Use
   `/api/v3/quotes/exactOut` (`QuotesV3Controller_getQuotes`) or
   `/api/v3/quotes/exactIn` (`QuotesV3Controller_getReverseQuotes`) if you need
   exact-output or exact-input pricing instead.

2. **Initiate the gasless intent** — `POST /api/v3/quotes/initiateGaslessIntent`
   (`QuotesV3Controller_initiateGaslessIntent`). Submit the accepted quote plus
   the required Permit (Permit2/Permit3) signatures so the transfer executes
   without the end user paying gas. Capture the returned intent hash.

3. **Track to fulfillment** — `POST /api/v3/intents/intentStatus`
   (`IntentsController_getIntentStatus`). Poll with the intent hash until the
   intent reaches a terminal state. Typical fulfillment is 20-40 seconds.

## Notes
- Solvers compete to fulfill the intent and front capital on the destination chain.
- If an intent cannot be fulfilled, check `refundableIntents`
  (`IntentsController_getRefundableIntents`) to recover funds.
