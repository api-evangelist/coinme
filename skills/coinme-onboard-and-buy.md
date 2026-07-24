---
name: Onboard a customer and buy crypto
description: KYC-onboard an end user under a partner, quote a buy, and execute it against the quote.
api: openapi/coinme-caas-openapi.json
operations: [initiate-authorize, customer-onboarding, initiate-transactional-quote, initiate-buy-sell-transaction, get-transaction-history]
---

# Onboard a customer and buy crypto (Coinme CaaS)

Use the Coinme Crypto-as-a-Service API to bring a new end user onto a partner platform and execute a buy.

## Auth
1. `initiate-authorize` — POST `/authorize` with HTTP Basic (partnerId=username, clientsecret=password) and your partner `User-Agent` header. Cache the returned JWT (valid 60 min prod / 30 min staging) and send it as the `Authorization` bearer on every later call; also send `x-api-key` on CaaS operations.

## Steps
2. `customer-onboarding` — POST `/customeronboarding` to verify KYC and create the customer account if it does not already exist. Keep the returned `customerId`.
3. `initiate-transactional-quote` — POST `/cryptoquote` with a fiat `debitCurrencyCode` (e.g. USD) and a crypto `creditCurrencyCode` (e.g. BTC) to get an executable quote. The `quoteId` expires (default 30s) — execute promptly.
4. `initiate-buy-sell-transaction` — POST `/transaction` with the `customerId` and `quoteId` to execute the buy at the exact quoted amounts and fees.
5. `get-transaction-history` — GET `/transactionhistory` for the customer to confirm settlement.

## Rules
- The quote binds execution: submit the `quoteId` before it expires or re-quote (see conventions/coinme-conventions.yml — quote-bound execution, no idempotency key).
- The Coinme Risk SDK must be integrated client-side for transactional services.
- Handle 401 by re-running `initiate-authorize` (token expiry); 400 is a validation error (see errors/coinme-problem-types.yml).
- Subscribe to `CAAS` webhooks for asynchronous buy/sell status (asyncapi/coinme-webhooks.yml).
