---
name: Sell crypto and withdraw to an external wallet
description: Quote and execute a sell, then send crypto from a customer's Coinme wallet to an external address.
api: openapi/coinme-caas-openapi.json
operations: [initiate-authorize, get-crypto-asset-balance, initiate-transactional-quote, initiate-buy-sell-transaction, get_walletsestimateFee, send]
---

# Sell crypto and withdraw to an external wallet (Coinme CaaS)

## Auth
1. `initiate-authorize` — obtain and cache the JWT (see the onboard-and-buy skill for the Basic→JWT flow); send `Authorization` bearer + `x-api-key`.

## Steps
2. `get-crypto-asset-balance` — GET `/cryptoassetbalance` to read the customer's available crypto assets and balances.
3. `initiate-transactional-quote` — POST `/cryptoquote` with crypto `debitCurrencyCode` and fiat `creditCurrencyCode` to create a sell quote (`quoteId`).
4. `initiate-buy-sell-transaction` — POST `/transaction` with `customerId` + `quoteId` to execute the sell.
5. `get_walletsestimateFee` — GET `/wallets/estimateFee` to estimate the blockchain network fee before a send.
6. `send` — POST `/send` to send a specified amount of crypto to an external wallet address.

## Rules
- Execute the `quoteId` before expiry (default 30s).
- Verify the destination address and chain; sends are irreversible on-chain actions.
- Watch `CAAS_BLOCKCHAIN` webhooks for on-chain settlement (asyncapi/coinme-webhooks.yml).
- Re-authorize on 401; inspect 400 validation errors (errors/coinme-problem-types.yml).
