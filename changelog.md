# Launch Draft Summary

ÐMP v1.0 is the first Dogenals Marketplace Protocol launch draft. The spec has not launched on-chain and does not
carry compatibility obligations from earlier internal drafts.

## Included

- Marketplace intents: `list`, `bid`, `auction`, `offer`, `counteroffer`, `accept`, `decline`, `cancel`, and `settle`.
- Provenance operations: `transfer`, `collection`, and `collection-update`.
- Creator-signed manifests and deterministic canonical JSON signatures.
- UTXO-native ownership verification by address-match against indexed inscription state.
- Settlement verification against real Dogecoin transaction outputs.
- Optional `parent_inscription_id` on root `collection`, aligned with DRC-721 native parent tags.
- ÐMP DogeTag Offers as the Era 2 OP_RETURN signaling extension.

Future changes after public launch should be tracked here with clear versioning and migration guidance.

## Draft updates (pre-launch)

### 2026-08-05 — Collaborative marketplace fees

- [collaborative-fees.md](collaborative-fees.md): multi-venue rake (listing + settlement) + royalty split physics.
- list schema: `market_fee_bps`, `listing_fee_address`, `listing_fee_share_bps`, `listing_marketplace`.
- Ecosystem: `dogenals/docs/OPERATOR_COLLECTIVE_PLAN.md`.

**Hardening status:** The full pre-launch hardening patch (MUST-099 through MUST-108, `bid_id` / `expiry` / `co_sellers` / `list_id`–`auction_id` alignment) is **live** in `spec.md`, `security.md`, examples, and `vectors/conformance.json` for this draft.

### 2026-05-08 — Verification hardening (editorial / v1.0 draft)

- **MUST-008**: global rules for positive koinu strings (no leading zeros; ≤ 2^64 − 1).
- **Settle**: **MUST-096** (buyer ≠ seller), **MUST-097** (`list_id` / `auction_id` inscription match),
  **MUST-098** (listing `seller` match when those refs present); §4.3 notes on safe royalty multiplication.
- **MUST-099–MUST-101**: `bid_id` → `offer` / `accept` resolution, optional offer `list_id`/`auction_id`
  linkage vs settle, buyer must match root offer buyer.
- **MUST-102–MUST-103**: §3.3 timestamp sanity (±2h vs MTP; use inscription `ts` for comparisons per rules).
- **MUST-104–MUST-106**: optional **`co_sellers`** on `list`/`auction`; cancel input rules after bids/offers;
  **`reason`** enum on `cancel` with legacy note.
- **MUST-107**: zero royalty ⇒ `royalty_paid` zero/absent (list/auction/collection basis).
- **MUST-108**: offer `expiry` vs MTP at settle confirmation.
- **Reorgs**: explicit requirement to downgrade settles/transfers if `settlement_txid` / `transfer_txid` drops
  from the best chain.
- **§15.3–§15.4**: settlement reference, cancel, and timestamp edge cases.
- **§10**: removed duplicate immutable-field bullet.
- **Design record**: the pre-launch hardening patch is integrated; normative
  text is in this spec.
