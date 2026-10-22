---
title: "HTTP 402"
description: "For decades, HTTP 402 Payment Required has been reserved for future use. x402 unlocks it, and [absolves the internet of its original sin](https://economyofbits.substack.com/p/marc-andreessens-original-sin)."
---

### What is HTTP 402?

[HTTP 402](https://datatracker.ietf.org/doc/html/rfc7231#section-6.5.2) is a standard, but rarely used, HTTP response status code indicating that payment is required to access a resource.

In x402, this status code is activated to:

* Inform clients (buyers or agents) that payment is required.
* Communicate the details of the payment, such as amount, currency, and destination address.
* Provide the information necessary to complete the payment programmatically.

### Why x402 Uses HTTP 402

The primary purpose of HTTP 402 is to enable frictionless, API-native payments for accessing web resources, especially for:

* Machine-to-machine (M2M) payments (e.g., AI agents).
* Pay-per-use models such as API calls or paywalled content.
* Micropayments without account creation or traditional payment rails.

Using the 402 status code keeps x402 protocol natively web-compatible and easy to integrate into any HTTP-based service.

### Payment Headers in V2

x402 V2 uses three standardized headers for payment communication:

| Header | Direction | Description |
|--------|-----------|-------------|
| `PAYMENT-REQUIRED` | Server → Client | Base64-encoded `PaymentRequired` object |
| `PAYMENT-SIGNATURE` | Client → Server | Base64-encoded `PaymentPayload` object |
| `PAYMENT-RESPONSE` | Server → Client | Base64-encoded `SettlementResponse` object |

* **`PAYMENT-REQUIRED`**: Contains the Base64-encoded payment requirements from the server. This header is returned alongside the 402 status code and includes details such as accepted payment schemes, price, network, and destination address.
* **`PAYMENT-SIGNATURE`**: Contains the Base64-encoded payment payload from the client. This header is sent by the client when retrying a request after receiving a 402 response, proving they have authorized payment.
* **`PAYMENT-RESPONSE`**: Contains the Base64-encoded settlement response from the server. This header is returned by the server after attempting settlement, whether successful or failed, providing structured feedback about the payment outcome.

All headers contain valid Base64-encoded JSON strings. This encoding ensures compatibility across different HTTP implementations and prevents issues with special characters in JSON payloads.

Whether funds move **onchain** in the same HTTP round trip depends on the **scheme**: **`exact`** and **`upto`** typically settle immediately, while **`batch-settlement`** confirms the payment authorization up front and redeems value **onchain** later according to the network binding (see **[Batch settlement](/schemes/batch-settlement)**).

### Browser clients

None of the three payment headers (`PAYMENT-REQUIRED`, `PAYMENT-SIGNATURE`, `PAYMENT-RESPONSE`) are CORS-safelisted. A cross-origin `fetch()` from a browser therefore cannot read `PAYMENT-REQUIRED` or `PAYMENT-RESPONSE` unless the server exposes them, and cannot send `PAYMENT-SIGNATURE` unless the preflight allows it.

A resource server intended to be reachable from a browser **should**:

- Send `Access-Control-Allow-Origin` for the calling origin on the 402 response itself. Without it the browser hides the entire response, and nothing else in this list has any effect.
- Name `PAYMENT-REQUIRED` and `PAYMENT-RESPONSE` in `Access-Control-Expose-Headers`, merged with any value already present rather than replacing it. A value of `*` does not cover them on a credentialed request.
- Name `PAYMENT-SIGNATURE` and `Content-Type` in `Access-Control-Allow-Headers`. `Content-Type` is safelisted only for form and plain-text values, so a payment request with a JSON body triggers the preflight on `Content-Type` before any payment header is considered — omit it and the client never reaches the 402 at all. A value of `*` does not cover either header on a credentialed request.
- Name the v1 spellings too, if it still serves v1 clients, each in the list matching its direction: `X-PAYMENT` in `Access-Control-Allow-Headers` (client sends it) and `X-PAYMENT-RESPONSE` in `Access-Control-Expose-Headers` (server returns it). A dual-version endpoint that names only one spelling fails silently for the other version's clients.
- Answer the preflight `OPTIONS` without requiring payment, and send a non-trivial `Access-Control-Max-Age`. A priced resource otherwise pays a preflight round trip on every cross-origin call.
- Send `Vary: Origin` if it reflects an origin rather than sending `*`, and keep the 402 out of shared caches.

**Diagnosing failures:** Omitting the expose list produces a 402 whose price the client cannot read — the response arrives, the header is hidden, and the client reports a parse failure on an apparently empty challenge. Omitting the allow list produces a payment the client cannot send — the browser refuses the retry at the preflight and reports a network error with no status. Neither message names CORS, and an unpaid probe with `curl` passes either way, so neither is diagnosable from the client side alone.

Test the preflight against the request the client's SDK actually sends, not just against the three protocol headers. Client SDKs may add extra request headers; a browser lists every non-safelisted name in `Access-Control-Request-Headers`, so an allow list naming only the protocol headers will refuse a retry that includes any additional header.

A resource server **may** instead refuse browser origins on a paid route. This is a choice about who may call the resource, not a payment control: CORS governs whether a page may read a response, while the decision to spend belongs to whatever holds the payer's signing key.

### Summary

HTTP 402 is the foundation of the x402 protocol, enabling services to declare payment requirements directly within HTTP responses. It:

* Signals payment is required
* Communicates necessary payment details
* Integrates seamlessly with standard HTTP workflows
