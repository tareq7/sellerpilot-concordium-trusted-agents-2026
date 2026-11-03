# Concordium Trusted Agents — event-specific implementation plan

**Event:** [Encode Club Concordium Trusted Agents Hackathon](https://www.encodeclub.com/programmes/concordium-trusted-agents-hackathon)  
**Project on Encode Club:** `SellerPilot Trusted Agents — Concordium` (one-person team created November 3, 2026)  
**Current status:** Registered / Checkpoint 1 project created. **Not a final submission; no working Concordium integration or on-chain proof yet.**  
**Existing application:** [SellerPilot AI](https://github.com/tareq7/sellerpilot-ai), built for an earlier Meta hackathon. All historical features and media are prior work.

## Problem and intended new contribution

Merchant agents can perform useful research and draft decisions, but another agent or API needs evidence that the calling agent is registered, accountable and authorised to act. The new Concordium-specific contribution will be a **fail-closed, privacy-preserving trust gate** for SellerPilot's synthetic, read-only inventory decision flow.

A requesting agent will present a **Concordium testnet CIS-8004 agent registry identity** and an Agent Card. The verifier will resolve the agent through Concordium's official hosted Agent Registry MCP or chain-backed REST service, confirm the active on-chain record and card-hash integrity, and make an explicit allow/deny decision. No API will execute marketplace writes or move real funds. If a separate **verified-human credential** is required, only a genuine supported issuer/identity-provider proof may satisfy that condition: an active registry badge **alone is not proof that a particular natural person completed identity verification**.

## Architecture to implement in the event's build window

```text
SellerPilot synthetic decision request
  -> human-approved scope + synthetic-data-only checks
  -> caller presents Concordium Agent Registry badge/token address
  -> read-only hosted MCP / REST resolver on CONFIRMED TESTNET
  -> validate Active status + trusted registry + agent-card SHA-256
  -> optionally validate separate ID-provider-backed human accountability proof
  -> ALLOW: read-only inventory recommendation with proof reference
   / DENY: structured reason (unknown, revoked, mismatch, stale, unavailable)
  -> append bounded, non-PII audit event for user inspection
```

The core gate should use an allowlisted **testnet** registry and never silently fall back to mainnet or an unchecked response. A Solana/CIS-8 external-key link or x402 testnet payment is a **stretch goal**, not a substitute for the core verified registry flow. No existing Solana or PayPal test payment can be presented as a Concordium testnet transaction.

## Evidence and acceptance tests

| Gate | Required check before claiming completed |
|---|---|
| Registered agent | Genuine CIS-8004 testnet registration transaction, explorer record, owner and token ID from official testnet tooling |
| Valid Agent Card | Public, immutable/versioned card URL; fetched byte-for-byte SHA-256 matches the on-chain `metadata_hash` |
| Badge | Active registry status, trusted network and contract, successful official badge verifier result |
| Verified human | Genuine issuer-backed identity/accountability proof and user consent where applicable; **never infer from badge alone** |
| Access decision | Real allowed read-only synthetic request; denied absent badge, revoked identity, mismatched hash, wrong network and expired proof |
| Failure safety | Offline/timeouts/rate limits return deny/unknown; no fail-open; no real marketplace writes or automatic financial transactions |
| Tests | Unit + integration tests with explicitly labelled mocks; at least one real read-only testnet lookup and an independently verified explorer record |
| Demo | New `<=3:00` video showing the actual successful and rejected testnet verification and explaining what is proved |
| Reproducibility | Public source, run instructions, public testnet endpoints, CCDExplorer link, mainnet transition and security/privacy limitations |

## Schedule

- **November 2–18, 2026 — learn/prepare:** attend the Concordium identity, agent registry and x402 workshops; inspect SDKs and identify the precise testnet contract and hosted resolver; draft acceptance tests and architecture. Documentation changes are preparation, **not** a claim that event-period integration has been built.
- **November 19 — official testnet build window starts:** implement the trust gate in this event-specific repository; use disposable testnet-only accounts and no real funds.
- **November 22, 23:59 UTC — Checkpoint 1 closes:** existing project/team must remain visible in Encode Club. Portal currently states no separate checkpoint form is required.
- **December 9, 23:59 UTC — final submission:** real testnet evidence, public code, CCDExplorer proof, demo of no more than three minutes and a credible production plan. Reopen the submitted portal form and verify saved status; a GitHub repo alone is not an organizer submission.

## Safety and external dependencies

- Concordium registration **requires an owner-controlled wallet signature**. Any real identity-provider verification requires that person's informed participation. Neither is authorized merely by creating the hackathon project; avoid automated KYC, private-key access, production identities or personal documents.
- Official MCP transaction tools return **sign-ready payloads**; their existence does not prove anything was submitted or finalized. Public resolver responses are untrusted until pinned to the correct testnet and rechecked.
- Keep third-party API keys, wallet keys, identity attributes, contact records and private operational tracking outside public GitHub and demo media. Do not register someone else's identity or link an existing production wallet without explicit authorization.
- Any optional x402 fee sponsorship must be tested and separately verified; do not assume it based on Solana.
- **No verified human badge, Concordium agent NFT, on-chain action, explorer link or final submission exists yet for this project.**

## Source references

- [Official event rules and schedule](https://www.concordium.com/article/concordium-trusted-agents-hackathon-encode-club)
- [Concordium Agent Registry and CIS-8004/CIS-8](https://docs.concordium.com/en/mainnet/technical-reference/agent-registry/index.html)
- [Official hosted MCP server and REST badge checks](https://docs.concordium.com/en/mainnet/technical-reference/agent-registry/mcp-service.html)
- [Verified by Concordium badge specification](https://docs.concordium.com/en/mainnet/technical-reference/agent-registry/concordium-badge.html)
- [Agent registration guide](https://docs.concordium.com/en/mainnet/how-to/integrations/agent-registry.html)
