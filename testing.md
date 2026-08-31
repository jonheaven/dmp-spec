# Testing Plan

This project is now in active implementation mode.

The goal of testing is simple: multiple independent indexers should be able to read chain data alone and converge on the same ÐMP market state.

## Where To Test

- Preferred: Dogecoin testnet if inscription tooling is available.
- Fallback: low-value mainnet testing with intentionally small DOGE amounts.
- Use throwaway wallets and test inscriptions where possible.

## Basic Process

1. Create or obtain low-value test inscriptions.
2. Inscribe ÐMP ops in a known order.
3. Record txids, inscription ids, timestamps, and expected final state.
4. Run at least two independent indexers against the same chain data.
5. Compare whether both indexers reach the same active listings, offers, auctions, settlements, and provenance-gap flags.

## Suggested Test Cases

### 1. Fixed-Price Listing

- Inscribe `list`
- Verify listing becomes active
- Spend inscription UTXO without settle
- Verify auto-invalidation

### 2. Auction - time_based

- Inscribe `auction` with `auction_mode = time_based`
- Add multiple bids
- Settle only after expiry
- Verify winner and final state

### 3. Auction - seller_can_accept_early

- Inscribe `auction` with `auction_mode = seller_can_accept_early`
- Add bid before expiry
- Settle before expiry
- Verify indexers accept the early settlement

### 4. Auction - no_early_accept

- Inscribe `auction` with `auction_mode = no_early_accept`
- Attempt pre-expiry settle
- Verify indexers reject it
- Settle after expiry and confirm valid result

### 5. Private Offer Negotiation

- Inscribe `offer`
- Inscribe `counteroffer`
- Inscribe `accept`
- Inscribe `decline` on a separate branch if needed
- Verify only one terminal negotiation path remains valid

### 6. Settlement With PSDT/PSBT Workflow

- Keep PSBT/PSDT transport off-protocol
- Broadcast final Dogecoin settlement transaction
- Inscribe `settle` with `settlement_txid`
- Verify indexers validate settlement against the confirmed transaction

### 7. Collection Manifest

- Inscribe `collection`
- If applicable, inscribe `collection-update`
- Verify collection signature rules and membership handling match across indexers

### 8. Raw Transfer / Provenance Gap

- Move inscription ownership without valid ÐMP settlement path
- Verify indexers flag `provenance_gap`
- Confirm both indexers report the same gap state

## Expected Outcome

Successful testing means:

- independent indexers converge on the same market state
- settlement validity matches on-chain transaction reality
- provenance-gap detection is consistent
- collection and negotiation behavior is deterministic

If two independent implementations disagree, treat that as a spec or implementation bug and resolve it before broader production rollout.