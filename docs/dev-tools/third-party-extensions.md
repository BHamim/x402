---
title: "Third-Party Extensions"
description: "Community packages that implement x402 protocol extensions or provide adjacent utilities for x402 clients and servers."
---

Extensions are a canonical x402 concept introduced in [x402 v2](../../specs/x402-specification-v2.md). Resource servers advertise supported extensions on `PaymentRequired`, clients echo them on `PaymentPayload`, and normative extension definitions live under [specs/extensions](../../specs/extensions/).

The tables below split community packages by how they relate to that mechanism. Submit a PR to this page when adding a listing.

## Interoperable x402 extensions

Packages that implement an x402 protocol extension: they declare extension data on `PaymentRequired` / `PaymentPayload` (the `extensions` field) and integrate with the canonical SDK extension hooks (`registerExtension`, `declare*Extension`, or equivalent) in [@x402/*](https://www.npmjs.com/org/x402) TypeScript, Go, or Python SDKs.

| Name | Description | Languages | Links |
| ---- | ----------- | --------- | ----- |
| [World AgentKit](https://docs.world.org/agents/agent-kit/integrate) | Verify human-backed agents | TypeScript | [GitHub](https://github.com/worldcoin/agentkit) · [npm](https://www.npmjs.com/package/@worldcoin/agentkit) |

## Third-party utilities

Tools that sit beside or wrap x402 clients and servers without registering a protocol extension namespace—pre-payment guards, monitoring, receipt verification, refund orchestration, and similar helpers. They may consume x402 traffic or artifacts but do not populate the `extensions` field as an interoperable extension implementation.

| Name | Description | Languages | Links |
| ---- | ----------- | --------- | ----- |
| [OMATrust](https://www.omatrust.org/x402) | Key authorizations for Signed Offers and Receipts | TypeScript | [GitHub](https://github.com/oma3dao/omatrust-sdk) · [npm](https://www.npmjs.com/package/@oma3/omatrust) |
| [PEAC Protocol](https://x402.peacprotocol.org) | Verifiable receipts for x402 payments | TypeScript | [GitHub](https://github.com/peacprotocol/peac) · [npm](https://www.npmjs.com/package/@peac/adapter-x402) |
| [x402r](https://x402r.org) | Non-custodial refund and arbitration protocol | TypeScript | [GitHub](https://github.com/BackTrackCo/x402r-sdk) · [npm](https://www.npmjs.com/package/@x402r/sdk) |
| [zauth](https://zauthx402.com) | Monitoring, verification, and refund SDK | TypeScript | [GitHub](https://github.com/zauthofficial/zauthSDK) · [npm](https://www.npmjs.com/package/@zauthx402/sdk) |
| [x402aff](https://github.com/MiroShark/x402aff) | Affiliate builder-code revenue splits | Python, TypeScript | [GitHub](https://github.com/MiroShark/x402aff) · [PyPI](https://pypi.org/project/x402aff/) · [npm](https://www.npmjs.com/package/x402aff) |
| [Tanilo x402 Guard](https://tanilo.io/docs/x402-guard) | Pre-payment check of price and spend cap against the buyer's own terms, using published rules; optional signed receipts | JavaScript | [GitHub](https://github.com/TKCollective/tanilo-x402-guard) · [npm](https://www.npmjs.com/package/@tanilo/x402-guard) |
| [Hosannaith](https://hosannaith.com) | Reliability oracle for x402 endpoints — verified uptime, correct-402, and latency scores on Algorand | TypeScript | [GitHub](https://github.com/cameron4316/x402-oracle-server) · [npm](https://www.npmjs.com/package/hosannaith-client) |
| [Elara](https://navigatorbuilds.github.io/elara-mesh/) | Post-quantum (ML-DSA-65) record verification; attestation bound to the x402 payment id | Rust | [GitHub](https://github.com/navigatorbuilds/elara-mesh) · [crates.io](https://crates.io/crates/elara-verify) |
