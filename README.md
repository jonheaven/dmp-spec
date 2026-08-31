# ÐMP — Dogenals Marketplace Protocol

**This repository is the canonical public specification for ÐMP (`p: "Ð:MP"`).**

ÐMP is the on-chain *record* of market intent: listings, auctions, bids, offers, settlements, transfers, and collection governance. Any indexer with a full node can *verify* those intents against UTXO movement; no venue’s API is the truth.

The **seller-signed PSDT** is the fill contract (Satoshi). The **inscription envelope** is the pointer Casey-style indexers already scan. Do not inscribe implied facts (`currency`, `chain`, default `listing_type`). Pay listing venues with `listing_fee_address`, not a domain string. All ops remain.

| | |
| ---: | --- |
| **Version** | v1.0+ · v1.0 [additive `parent_inscription_id`](spec.md#v13-2026-04-25) |
| **Class** | Marketplace **intent and proof** layer (not custody, not a fungible token) |
| **Payload** | JSON in a Dogecoin inscription with `"p": "Ð:MP"` |
| **Status** | v1.0 Launch Draft |
| **Chain** | Dogecoin |
| **License** | [MIT](LICENSE) |
| **Source of truth** | **[spec.md](spec.md)** (normative) — this file orients; it does not override `MUST` rules there |

*Peer analogue:* Bitcoin inscriptions with marketplace JSON — ÐMP is the [Dogecoin](https://github.com/dogecoin/dogecoin) native rulebook for that class of application.

> ÐMP is **not** a token standard. It does not create fungible tokens. It governs how NFT (Doginal) trades are recorded, verified, and discovered on-chain.

## Authors

Jon Heaven — GitHub [@jonheaven](https://github.com/jonheaven) · X [@jontype](https://x.com/jontype)

If you ship this in a wallet, marketplace, or indexer, you are implementing **this** protocol. Keep `"p": "Ð:MP"` and the op shapes identical so every indexer sees one order book.

## Start here (pick your job)

| You are… | Read this |
| --- | --- |
| **Human, 2 minutes** | this README |
| **Indexer / marketplace engineer** | **[implementation-guide.md](implementation-guide.md)** |
| **Protocol implementer** | **[spec.md](spec.md)** — normative rules |
| **Need JSON today** | **[EXAMPLES/](EXAMPLES/)** + **[schemas/](schemas/)** |
| **Reference parser** | **[reference/dmp_reference.py](reference/dmp_reference.py)** |

---

## Position in the Dogenals Ecosystem

ÐMP is the marketplace sub-protocol under the Dogenals umbrella. It focuses exclusively on NFT marketplace operations and is the only Dogenals protocol designed for this purpose.

| What ÐMP Does | What ÐMP Does NOT Do |
| --- | --- |
| Records listings, bids, auctions on-chain | Issue or transfer fungible tokens |
| Provides trustless settlement verification | Custody funds or NFTs |
| Enables shared liquidity across marketplaces | Enforce royalties on-chain |
| Closes provenance gaps with transfer ops | Replace existing marketplace UX |

### DogeRelics Core and native parent

ÐMP JSON lives in the inscription. **DogeRelics Core** defines an *ord* script **tag trailer** (after the last body push with countdown 0) for native `parent`, `delegate`, and `properties` on Dogecoin, [aligned with PRC-721](https://peppool.space/docs/prc-721). From v1.0, a **root** ÐMP `collection` **MAY** set optional `parent_inscription_id` for metadata; it **MUST** match a native DogeRelics `parent` when both are present, per the [ÐMP v1.0](spec.md#v13-2026-04-25) spec.

---

## Key Features

**Trustless settlement.** Any indexer can independently verify that a settlement is valid — that the inscription moved to the buyer and the seller received payment — using only Dogecoin blockchain data.

**Multi-marketplace liquidity.** Multiple independent marketplaces can emit and consume ÐMP intents. A listing created on Marketplace A is visible to Marketplace B and all aggregators.

**Full provenance chain.** Every on-chain owner change is tracked. Provenance gaps (inscriptions transferred without a ÐMP op) are detected and flagged transparently.

**Rich marketplace operations.** ÐMP supports fixed-price listings, time-based auctions, private offers, counter-offers, and acceptance/decline flows — all on-chain.

**Signature-based authorization.** Operations are cryptographically authorized using Dogecoin signing, eliminating the need for smart contracts.

**DogeTag offer signaling.** Optional [ÐMP DogeTag Offers](dogetag-offers.md) use lightweight OP_RETURN signals plus small recipient DOGE outputs so wallets can detect buy interest even when a user is not connected to a marketplace.

**DOTC receipts.** Completed live / private OTC deals MAY emit DOTC (`dotc|1|…` in `OP_RETURN` on the inscription-move tx) without a ÐMP inscription. A later ÐMP `settle` MAY reference that DOTC txid. DOTC is not a listing protocol.

---

## Protocol Summary

| Field | Value |
| --- | --- |
| Protocol marker | `"p": "Ð:MP"` |
| Supported versions | `"1.0"` |
| Storage method | Ordinal inscription |
| Data format | JSON |
| Signature scheme | Dogecoin signed message (secp256k1 + double-SHA256) |

---

## Supported Operations

| Operation | Purpose |
| --- | --- |
| `list` | Seller declares intent to sell at fixed price |
| `bid` | Buyer declares intent to purchase |
| `settle` | Records completed transfer + payment verification |
| `cancel` | Revokes any active intent (list, bid, offer, counteroffer) |
| `auction` | Creates an auction with configurable modes |
| `offer` | Private push-style offer from buyer to seller |
| `counteroffer` | Seller responds with alternate terms |
| `accept` | Accepts an offer or counteroffer |
| `decline` | Declines an offer or counteroffer |
| `transfer` | Explicit non-sale ownership transfer (gifts, airdrops) |
| `collection` | Creator-signed collection manifest |
| `collection-update` | Append-only metadata update for a collection |
| `vote` | DAO governance signal |

---

## Documentation

| Document | Description |
| --- | --- |
| [spec.md](spec.md) | Full protocol specification |
| [dogetag-offers.md](dogetag-offers.md) | Lightweight OP_RETURN offer signaling extension |
| [implementation-guide.md](implementation-guide.md) | How to build a compliant indexer |
| [security.md](security.md) | Threat model and mitigations |
| [adoption-roadmap.md](adoption-roadmap.md) | Phased adoption strategy |
| [philosophy-and-design.md](philosophy-and-design.md) | Design principles and philosophy |
| [collaborative-fees.md](collaborative-fees.md) | Multi-venue rake + royalty settlement physics |
| [changelog.md](changelog.md) | Version history |
| [contributing.md](contributing.md) | How to contribute |
| [testing.md](testing.md) | Testing approach and scenarios |
| [EXAMPLES/](EXAMPLES/) | JSON operation examples (incl. `collection-with-parent-inscription.json` for v1.0) |
| [schemas/](schemas/) | JSON Schema validation files |
| [vectors/](vectors/) | Conformance vectors |
| [reference/](reference/) | Python reference validator |

---

## Quick Example

A ÐMP `list` inscription:

```json
{
  "p": "Ð:MP",
  "op": "list",
  "inscription_id": "<txid>i0",
  "seller": "DLfZ6VN5k7EyqjkqXZzd6mh6nQQZL1262b",
  "price": "100000000",
  "psdt": "cHNidP8B..."
}
```

A ÐMP `settle` inscription (6+ blocks after the transfer):

```json
{
  "p": "Ð:MP",
  "v": "1.0",
  "op": "settle",
  "list_id": "list_20260401_abc123",
  "inscription_id": "abc123...i0",
  "buyer": "DEW85...y28uN",
  "seller": "DLfZ6VN5k7EyqjkqXZzd6mh6nQQZL1262b",
  "price": 100000000,
  "settlement_txid": "def456...",
  "royalty_amount": 5000000,
  "royalty_recipient": "DArtist1...",
  "ts": 1743465900
}
```

---

## Reference Implementation

A Python reference validator lives in [reference/](reference/). It tracks the launch-draft validation pipeline and includes test vectors.

```bash
python reference/dmp_reference.py validate EXAMPLES/list-op.json
python reference/dmp_reference.py test-vectors
```

---

## Repo layout

```text
spec.md                 normative protocol
implementation-guide.md indexer / marketplace behavior
security.md             threat model
EXAMPLES/               JSON operation examples
schemas/                JSON Schema files
vectors/                conformance vectors
reference/              Python reference validator
```

ÐMP is a sub-protocol of the Dogenals ecosystem. Studio working copy (private): `dogenals/spec/protocols/dmp/`.
