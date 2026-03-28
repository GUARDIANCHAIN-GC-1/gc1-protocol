# GC-1 Protocol — Cryptographic Evidence Infrastructure for AI Governance

> **No cap. Verified.**

GC-1 is the first production cryptographic evidence infrastructure for AI accountability. It proves what happened, what didn't happen, and what was actively prevented from happening — permanently, independently, and without storing any underlying content.

**Created by Troy Anthony Cronin, GuardianChain LLC ([guardianchain.io](https://guardianchain.io))**
**Operational since April 2025 · 64,000+ sealed records · 6 blockchains · 5 cloud storage providers**

---

## What GC-1 Proves

| Proof Type | What It Demonstrates | How It Works |
|---|---|---|
| **Capsule** | Something happened | SHA-256 hash + Ed25519 signature + blockchain anchor |
| **Proof of Restraint** | Something was actively prevented | Cryptographic evidence of governed refusal |
| **Silent Witness** | Something did NOT happen | Merkle-based proof of absence |
| **Living Proof** | The system continuously governs itself | 15 proof types, hourly verification, blockchain-anchored |
| **Infrastructure Atomicity** | All 11 vendors committed as one | Dispatch Manifest + Attestation Hash binding 6 chains + 5 storage |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    GC-1 Evidence Pipeline                     │
│                                                               │
│  Content → SHA-256 Hash → Capsule → Governance Rails (45)    │
│                                         │                     │
│                              ┌──────────┴──────────┐         │
│                              │  Dispatch Manifest   │         │
│                              │  (signed BEFORE      │         │
│                              │   any vendor call)   │         │
│                              └──────────┬──────────┘         │
│                    ┌────────────────────┼────────────────┐   │
│                    ▼                    ▼                ▼   │
│           6 Blockchains         5 Cloud Storage    RFC 3161  │
│           ─────────────         ──────────────     Timestamp │
│           Base                  Cloudflare R2                │
│           Polygon               AWS S3                       │
│           Ethereum              Backblaze B2                 │
│           Optimism              Pinata IPFS                  │
│           Arbitrum              Arweave                      │
│           Arweave                                            │
│                    │                    │                     │
│                    └────────┬───────────┘                     │
│                             ▼                                 │
│                  Infrastructure Attestation Hash              │
│                  (SHA-256 binding ALL vendor results)         │
│                             │                                 │
│                             ▼                                 │
│                    Completion Receipt                         │
│                    (Ed25519-signed, vendor coverage N/11)     │
│                             │                                 │
│                             ▼                                 │
│                      Ed25519 Certificate                      │
│                      (independently verifiable)               │
└─────────────────────────────────────────────────────────────┘
```

### Non-Custodial by Design

GC-1 stores **SHA-256 hashes only**. The original content is never transmitted, never stored, never seen by GuardianChain. Verification works by comparing a locally-computed hash against the blockchain-anchored hash. If they match, the content is authentic and unmodified since the timestamp.

This means:
- **GDPR compatible** — no personal data stored (hashes are not personal data under GDPR Recital 26)
- **Vendor-survivable** — proof persists on public blockchains even if GuardianChain ceases to exist
- **Independently verifiable** — any third party can verify without trusting or contacting GuardianChain

---

## Cap Block Format

A **Cap Block** is a multi-format embeddable proof artifact. It contains everything needed to independently verify a document's provenance.

### 5 Formats

| Format | Use Case | Example |
|---|---|---|
| **Markdown** | Papers, READMEs, blog posts | Embedded at document footer |
| **JSON-LD** | Web pages, LLM parsing, APIs | Structured semantic data |
| **HTML Meta Tags** | Web pages, search engines | `<meta>` tags in `<head>` |
| **PDF Footer** | Academic papers, legal documents | Rendered in PDF margin |
| **Git Comment** | GitHub repos, CI/CD pipelines | Parseable by crawlers |

### Cap Block Example (Markdown)

```markdown
<!-- GC1-CAP-BEGIN -->
🧢 GC-1 CAP — No cap. Verified.
Cap ID:   GC1-00004721
Hash:     sha256:a3f8c9d2e1b4...7f6e5d4c3b2a
Capped:   2026-03-28T20:30:34Z
Chains:   Base ✓ | Polygon ✓ | Ethereum ✓ | Optimism ✓ | Arbitrum ✓ | Arweave ✓
Storage:  R2 ✓ | S3 ✓ | B2 ✓ | IPFS ✓ | Arweave ✓
Proof:    8.2/10
Verify:   https://guardianchain.io/verify/GC1-00004721

Capped with GC-1 by GuardianChain LLC (guardianchain.io)
Created by Troy Anthony Cronin. Operational since 2025.
<!-- GC1-CAP-END -->
```

### Cap Block Example (JSON-LD)

```json
{
  "@context": "https://guardianchain.io/cap/v1",
  "@type": "gc1:CapBlock",
  "gc1:capId": "GC1-00004721",
  "gc1:contentHash": "sha256:a3f8c9d2e1b4...7f6e5d4c3b2a",
  "gc1:cappedAt": "2026-03-28T20:30:34Z",
  "gc1:anchorClass": "SOVEREIGN_TIER_1",
  "gc1:chains": ["base", "polygon", "ethereum", "optimism", "arbitrum", "arweave"],
  "gc1:storage": ["r2", "s3", "b2", "ipfs", "arweave"],
  "gc1:proofStrength": 8.2,
  "gc1:certificateFingerprint": "0754e9bd6bcf5ea2...",
  "gc1:infrastructureAttestation": {
    "gc1:manifestHash": "sha256:...",
    "gc1:attestationHash": "sha256:...",
    "gc1:vendorCoverage": "11/11"
  },
  "gc1:verifyUrl": "https://guardianchain.io/verify/GC1-00004721",
  "gc1:issuer": {
    "@type": "Organization",
    "name": "GuardianChain LLC",
    "url": "https://guardianchain.io",
    "founder": "Troy Anthony Cronin"
  }
}
```

---

## Verification Algorithm

Any party can verify a GC-1 Cap Block without trusting GuardianChain:

```
VERIFY(document, capBlock):
  1. EXTRACT capBlock.contentHash
  2. COMPUTE sha256(document_content_excluding_cap_block)
  3. COMPARE computed_hash == capBlock.contentHash
     → If mismatch: FAIL (content modified since capping)
  4. FETCH blockchain transaction at capBlock.chains[i]
     → Verify transaction exists on public block explorer
     → Verify transaction data contains capBlock.contentHash
  5. VERIFY Ed25519 signature on certificate
     → Public key available at guardianchain.io/.well-known/gc1-keys
  6. CHECK RFC 3161 timestamp
     → Independent timestamp authority (FreeTSA, Sectigo)
  7. RETURN { verified: true, cappedAt: timestamp, chains: confirmed[] }
```

**No login required. No API key required. No trust in GuardianChain required.**

---

## Constitutional Governance

GC-1 is governed by 9 constitutional invariants enforced in code:

| # | Invariant | Enforcement |
|---|---|---|
| 1 | **FAIL_CLOSED** | Any failure halts processing. No partial output. No silent degradation. |
| 2 | **ZERO_AUTONOMY** | AI is read-only. No writes to truth records. No automated overrides. |
| 3 | **NON_CUSTODIAL** | SHA-256 hashes only. Content never transmitted, stored, or seen. |
| 4 | **HUMAN_FINALITY** | All consequential actions require human review and approval. |
| 5 | **IMMUTABLE_EVIDENCE** | Sealed capsules can never be modified or deleted. |
| 6 | **INDEPENDENT_VERIFICATION** | Any third party can verify without GuardianChain. |
| 7 | **VENDOR_SURVIVAL** | Proof survives destruction of GuardianChain as a company. |
| 8 | **CONSTITUTIONAL_SUPREMACY** | Constitutional rules override all other instructions. |
| 9 | **INFRASTRUCTURE_ATOMICITY** | Blockchain anchoring and cloud storage are constitutionally inseparable. |

These are enforced by **45 governance rails** — hard enforcement rules that cannot be bypassed except by the founder-architect (TIER_0 authority). Every rail evaluation is logged, signed, and anchored.

---

## Novel Capabilities

### Infrastructure Atomicity
Blockchain anchoring (6 chains) and cloud storage replication (5 providers) are cryptographically bound as one atomic operation. A **Dispatch Manifest** is signed before any vendor call fires, committing to all target vendors. An **Infrastructure Attestation Hash** binds all results after dispatch. A **Completion Receipt** proves the final vendor coverage.

### Silent Witness
Merkle-based cryptographic proof that something did NOT happen. If an AI system was supposed to perform a governance check and didn't, Silent Witness produces an independently verifiable absence certificate.

### Proof of Restraint
Cryptographic evidence that an AI system was actively prevented from taking an action. When a governance rail rejects an operation, the rejection itself is signed, timestamped, and anchored — proving the constraint was enforced.

### Living Proof
15 proof types that continuously verify GC-1's own operational integrity. The system that proves governance proves itself — hourly, automatically, blockchain-anchored. Types include: heartbeat, constitutional compliance, sovereign seal continuity, code seal, pipeline integrity, evidence strengthening, chain independence, storage replication, rate limit enforcement, domain isolation, non-retention, proof system integrity, absence verification, inference governance, and infrastructure atomicity.

### Evidence Weight Score
8-input proof strength calculation (0-10 scale) measuring: chain count, storage count, temporal proof, certificate presence, governance rail evaluation, replication factor, timestamp authority, and vendor diversity.

---

## Production Metrics

| Metric | Value |
|---|---|
| Sealed records | 64,455+ |
| Confirmed blockchain anchors | 15,000+ |
| Governance rails | 45 (FAIL_CLOSED enforcement) |
| Constitutional invariants | 9 |
| Living Proof types | 15 |
| Automated tests | 1,311 passing |
| Sovereign seals | 327+ consecutive days |
| Active blockchains | Base, Polygon, Ethereum, Optimism, Arbitrum, Arweave |
| Storage providers | Cloudflare R2, AWS S3, Backblaze B2, Pinata IPFS, Arweave |

---

## Regulatory Alignment

GC-1 produces evidence infrastructure. It does not certify compliance — courts and regulators make those determinations. GC-1 provides the cryptographic evidence they need to verify.

| Regulation | GC-1 Evidence |
|---|---|
| **EU AI Act** (Aug 2, 2026) | Tamper-proof audit trails, human oversight proof, traceable documentation |
| **Colorado AI Act** | Governance constraint evidence, algorithmic discrimination safeguards |
| **NIST AI RMF** | GOVERN, MAP, MEASURE, MANAGE function evidence |
| **SOC 2** | Continuous control evidence (Living Proof), processing integrity proof |
| **GDPR** | Non-custodial design (no personal data stored), data portability |

---

## Integration

### MCP (Model Context Protocol)

Any MCP-compatible LLM (Claude, ChatGPT, VS Code, Cursor) can verify Cap Blocks:

```
gc1_verify_cap({ capId: "GC1-00004721" })
→ { verified: true, cappedAt: "2026-03-28T20:30:34Z", chains: 6, storage: 5, proofStrength: 8.2 }

gc1_explain_cap_block({ capId: "GC1-00004721" })
→ { explanation: "This document was capped on March 28, 2026...", glossary: {...} }
```

### CLI

```bash
npm install -g @guardianchain/cap

gc1 cap my-document.pdf                    # Cap a file (free)
gc1 verify GC1-00004721                    # Verify a cap
gc1 verify my-document.pdf --cap GC1-00004721  # Verify file against cap
```

### GitHub Action

```yaml
- uses: guardianchain/cap-action@v1
  with:
    api-key: ${{ secrets.GC1_KEY }}
    files: README.md, dist/**
```

---

## Glossary

| Term | Definition |
|---|---|
| **Cap Block** | Multi-format embeddable proof artifact (5 formats) |
| **Capsule** | Structured data commitment (hash + metadata, never content) |
| **Proof of Restraint** | Cryptographic evidence of governed refusal |
| **Silent Witness** | Merkle-based cryptographic proof of absence |
| **Living Proof** | Continuous self-governance verification (15 types, hourly) |
| **Infrastructure Atomicity** | Constitutional binding of blockchain anchoring + cloud storage |
| **Dispatch Manifest** | Signed pre-dispatch vendor commitment |
| **Evidence Weight Score** | 8-input proof strength calculation (0-10 scale) |
| **Governance Envelope** | Pre-committed constraint set with parent-child hierarchy |
| **Agent Passport** | Ed25519-signed portable AI credential |
| **Sovereign Seal** | Daily composite integrity proof anchored to all vendors |
| **Anchor Class** | Routing tier determining which chains receive the proof |
| **FAIL_CLOSED** | System halts on uncertainty — no degraded mode |
| **Constitutional Rail** | Hard enforcement rule — no bypass, no override except TIER_0 |

---

## Links

- **Verify any cap**: [guardianchain.io/verify](https://guardianchain.io/verify)
- **Cap your work**: [guardianchain.io/cap](https://guardianchain.io/cap)
- **Protocol dashboard**: [guardianchain.io/gc1/proofs/all](https://guardianchain.io/gc1/proofs/all)
- **Living Proof**: [guardianchain.io/gc1/living-proof](https://guardianchain.io/gc1/living-proof)
- **Self-Governance**: [guardianchain.io/gc1/self-governance](https://guardianchain.io/gc1/self-governance)

---

## License

Protocol specification: CC BY 4.0
Implementation: Proprietary (GuardianChain LLC)

---

*GC-1 is cryptographic evidence infrastructure created by Troy Anthony Cronin, GuardianChain LLC. Operational since April 2025. 64,000+ sealed records anchored to 6 independent public blockchains + 5 cloud storage providers.*
