# GC-1 Protocol

**Cryptographic Evidence Infrastructure for AI Governance**

GC-1 is a production cryptographic evidence infrastructure that transforms AI governance from policy documentation into independently verifiable proof. Every governance decision, every compliance event, every human authorization is cryptographically sealed, independently timestamped, and anchored to multiple public blockchains.

**Production Status:** 21,000+ governance capsules sealed across 6 independent public blockchains.

---

## The Problem

Every AI governance platform today produces self-reported evidence. The company deploying AI writes its own compliance logs, generates its own risk assessments, and stores the results in databases it controls. When a regulator, auditor, or attorney asks for evidence, the company hands them documents the company created about itself.

This creates a fundamental trust problem: the evidence is only as trustworthy as the entity that produced it. Logs can be edited. Timestamps can be backdated. Records can be deleted. There is no way for a third party to independently verify that governance evidence is authentic, complete, and unmodified.

GC-1 solves this by making governance evidence **independently verifiable** — anchored to public infrastructure that nobody controls.

## How It Works

### Capsule Pipeline

Every governance event flows through the GC-1 capsule pipeline:

1. **Hash** — The governance event (decision, authorization, compliance check) is SHA-256 hashed. GC-1 receives only the hash, never the original content (non-custodial).
2. **Sign** — The hash is signed with Ed25519 digital signatures using a three-tier key hierarchy (root, certificate, operational).
3. **Timestamp** — An independent RFC 3161 Timestamp Authority (not controlled by GC-1) issues a legally recognized timestamp proving when the hash existed.
4. **Anchor** — The signed, timestamped hash is submitted to 6 independent public blockchains simultaneously: Base, Polygon, Ethereum, Optimism, Arbitrum, and Arweave.
5. **Certify** — When sufficient blockchain confirmations arrive, the capsule is sealed with an Ed25519-signed certificate.

### Verification

Any third party can verify a GC-1 capsule independently:

- Check the SHA-256 hash against the original document
- Verify the Ed25519 signature using the public key
- Confirm the RFC 3161 timestamp with the issuing authority
- Look up the blockchain anchors on any public block explorer (BaseScan, PolygonScan, Etherscan, Arbiscan, ViewBlock)

**No GC-1 cooperation required.** The evidence is verifiable even if GuardianChain ceases to exist.

## Architecture

### Anchor Class System

GC-1 supports three tiers of evidence anchoring:

| Anchor Class | Chains | Storage | Use Case |
|---|---|---|---|
| `SOVEREIGN_TIER_1` | All 6 blockchains + all 5 storage providers | Full redundancy | Defense, legal, autonomous systems, internal governance |
| `INSTITUTIONAL_REGULATED` | 3 blockchains + 3 storage providers | Balanced | Healthcare, financial services, regulated industries |
| `PUBLIC_PROOF` | 2 blockchains + 2 storage providers | Cost-optimized | Research integrity, academic, general proof |

### Constitutional Enforcement

GC-1 operates under four constitutional constraints enforced by code, not policy:

- **FAIL_CLOSED** — Every error halts processing. No silent failures. No degraded evidence.
- **ZERO_AUTONOMY** — AI is read-only verifier. Cannot write to truth records. Cannot override human decisions.
- **NON_CUSTODIAL** — GC-1 never stores original content. Only cryptographic hashes. Customer data is architecturally inaccessible.
- **HUMAN_FINALITY** — No system overrides human authority. Every autonomous action requires prior human authorization.

These constraints are enforced at 1,888+ code enforcement points verified by automated testing.

### Governance Rails

43 governance rails evaluate every capsule before certification. Each rail enforces a specific governance requirement. If any rail fails, the capsule is rejected (FAIL_CLOSED) and a signed Proof of Restraint is generated — cryptographic evidence that governance *prevented* an action.

### Evidence Weight Score

Each capsule receives an Evidence Weight score (0-10) based on:
- Number of blockchain confirmations
- Number of independent storage providers
- RFC 3161 timestamp presence
- Ed25519 signature validity
- Anchor class tier

A capsule anchored to all 6 chains with all 5 storage providers at SOVEREIGN_TIER_1 achieves Evidence Weight 10/10.

## Unique Capabilities

### Silent Witness — Proof of Absence

GC-1 can mathematically prove that a specific event did NOT occur within a governed time window. Using Merkle tree indexing with absence certificates, Silent Witness generates cryptographic proof of non-occurrence.

**Use case:** "Prove the AI did not access unauthorized patient records between 09:00 and 17:00."

No other platform offers negative evidence with cryptographic proof.

### Two-Phase Temporal Authority (Autonomous Decision Shield)

For autonomous systems (drones, vehicles, surgical robots, industrial automation), GC-1 provides two-phase temporal proof:

1. **Phase 1 (Authorization):** Human authorization is cryptographically sealed and blockchain-anchored.
2. **Phase 2 (Execution):** Autonomous execution is sealed and linked to the Phase 1 capsule.
3. **Governance Rail #44 (TEMPORAL_AUTHORITY_VERIFICATION):** Verifies that Phase 1 preceded Phase 2 with a minimum temporal delta per system type.

Post-hoc authorization fabrication is architecturally detectable because blockchain timestamps are immutable.

### Decision Chain Reconstruction

```
GET /api/truth/decision-chain/:systemId
```

Public API endpoint (no authentication required) that reconstructs the complete governance history of any autonomous system. Any congressional committee, federal judge, or attorney can query the full decision chain without GC-1 cooperation.

### Recursive Self-Governance

GC-1 governs itself with the same cryptographic rigor it demands of customers. Every deployment, boot attestation, sovereign seal, and governance rail enforcement generates a signed operational record anchored at SOVEREIGN_TIER_1. The stewardship manifest (an index of all operational records) is itself a signed, blockchain-anchored record — creating recursive integrity.

### Vendor Survival Proof

Complete Merkle trees are stored across 5 independent storage providers (Cloudflare R2, Backblaze B2, AWS S3, Arweave, IPFS). If GuardianChain ceases to exist, every piece of evidence remains independently verifiable using public blockchain explorers and distributed storage.

## Regulatory Alignment

GC-1 evidence infrastructure is designed for compliance with:

| Regulation | Jurisdiction | Enforcement Date |
|---|---|---|
| EU AI Act (Regulation 2024/1689) — Articles 9-14 | European Union | August 2, 2026 |
| Colorado AI Act (SB 24-205) | United States — Colorado | June 30, 2026 |
| FDA QMSR (21 CFR 820 update) | United States | February 2, 2026 |
| FRE 902(13)/(14) — Self-authenticating electronic records | United States (Federal) | Effective |
| DoD Directive 3000.09 — Autonomous weapons systems | United States — Defense | Effective |
| EU MDR (Regulation 2017/745) | European Union | Effective |
| NYC Local Law 144 — Automated hiring | United States — New York City | Effective |

## Integration

### REST API

```bash
# Create a governance capsule
curl -X POST https://gc1.guardianchain.com/api/capsules/v3/create \
  -H "Authorization: Bearer $GC1_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contentHash": "sha256-of-governance-event",
    "anchorClass": "SOVEREIGN_TIER_1",
    "metadata": {
      "type": "COMPLIANCE_CHECK",
      "framework": "EU_AI_Act",
      "article": "9",
      "result": "PASSED"
    }
  }'

# Verify a capsule (public, no auth required)
curl https://gc1.guardianchain.com/api/truth/verify/CAPSULE_ID

# Reconstruct a decision chain (public, no auth required)
curl https://gc1.guardianchain.com/api/truth/decision-chain/SYSTEM_ID
```

### MCP (Model Context Protocol) Integration

GC-1 includes an MCP server with 20 governance tools, enabling any MCP-compatible AI agent (Claude, GPT, Gemini) to submit governance evidence during tool execution.

### SDKs (Coming Q3 2026)

- **Node.js:** `npm install @guardianchain-prod/gc1-sdk`
- **Python:** `pip install guardianchain`

## Production Metrics

| Metric | Value |
|---|---|
| Total capsules sealed | 21,000+ |
| Blockchain anchors | 1,352+ (1,060+ confirmed) |
| Active blockchain chains | 6 (Base, Polygon, Ethereum, Optimism, Arbitrum, Arweave) |
| Governance rails | 43+ (zero violations) |
| Constitutional enforcement points | 1,888+ |
| Tests | 940+ (zero failures) |
| Storage providers | 5 configured |
| Sovereign seal | Continuous |

## Comparison

| Capability | GC-1 | OneTrust | Credo AI | IBM watsonx | VeritasChain |
|---|---|---|---|---|---|
| Blockchain-anchored evidence | ✅ 6 chains | ❌ | ❌ | ❌ | Spec only |
| Independent RFC 3161 timestamps | ✅ | ❌ | ❌ | ❌ | ✅ (spec) |
| Negative evidence (Silent Witness) | ✅ Production | ❌ | ❌ | ❌ | ❌ |
| Two-phase temporal authority | ✅ Production | ❌ | ❌ | ❌ | ❌ |
| Non-custodial (zero content storage) | ✅ | ❌ | ❌ | ❌ | ❌ |
| Vendor-independent verification | ✅ | ❌ | ❌ | ❌ | ❌ |
| Public decision chain API | ✅ | ❌ | ❌ | ❌ | ❌ |
| Self-governance at sovereign tier | ✅ | ❌ | ❌ | ❌ | ❌ |
| Constitutional enforcement (code) | ✅ 1,888 points | ❌ | ❌ | ❌ | ❌ |
| Production capsules | 21,000+ | N/A | N/A | N/A | 0 (demo only) |

## Documentation

- [How GC-1 Works](https://guardianchain.io/gc1/how-it-works)
- [What GC-1 Proves](https://guardianchain.io/gc1/what-it-proves)
- [Sovereign Seals](https://guardianchain.io/gc1/sovereign-seals)
- [Proof Explorer](https://guardianchain.io/gc1/explorer)
- [Verify a Capsule](https://guardianchain.io/verify)

## About GuardianChain

GuardianChain LLC builds GC-1, cryptographic evidence infrastructure for AI governance. GC-1 is Layer 5 infrastructure — it sits beneath existing governance platforms (OneTrust, Credo AI, IBM watsonx, Microsoft Purview) and makes their attestations independently verifiable.

- **Website:** [guardianchain.io](https://guardianchain.io)
- **Production:** [gc1.guardianchain.com](https://gc1.guardianchain.com)
- **Contact:** government@guardianchain.io

## License

MIT License — see [LICENSE](LICENSE) for details.

The GC-1 protocol specification and documentation in this repository are open. The production infrastructure is operated by GuardianChain LLC.
