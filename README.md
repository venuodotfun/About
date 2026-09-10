# Venuo

### Fair token sales on Solana.

Venuo is a protocol for **scheduled primary token sales on Solana** that separates contribution from token delivery.

Participants contribute during a sale window, then claim their fixed allocation after the sale closes — including to a wallet different from the one used to contribute.

> **Contribute now. Claim later. Choose where your allocation goes.**

[Website](https://venuo.fun) · [Documentation](https://venuo.fun/docs) · [Technical Paper](https://venuo.fun/docs/whitepaper)

---

## Why Venuo?

In a conventional token sale, the wallet that contributes is usually the wallet that receives the allocation.

That creates a direct onchain relationship:

```text
Funding wallet
      │
      │ contribution
      ▼
   Token sale
      │
      │ allocation
      ▼
Same wallet
```

Venuo separates those two events:

```text
Funding wallet
      │
      │ contribution + commitment
      ▼
   Venuo sale
      │
      │ finalize
      ▼
Finalized sale set
      │
      │ zero-knowledge membership proof
      ▼
Fresh destination
      │
      ▼
Token allocation
```

The contribution remains public. What changes is the direct mapping between a particular contribution commitment and the later claim.

---

## How it works

Venuo uses a five-step lifecycle.

### 1. Create a sale

A creator defines the sale parameters:

* Contribution asset
* Fixed contribution lot
* Token allocation per lot
* Sale capacity
* Contribution window
* Claim window
* Cancellation terms

The required token inventory is escrowed before contributions open.

### 2. Contribute

A participant contributes the required amount and creates a secret-derived commitment.

The contribution and commitment are recorded onchain, but the eventual claim destination is not selected at this stage.

### 3. Finalize

When the contribution window closes, Venuo freezes the eligible contribution set.

The final commitment-tree root becomes the canonical set from which claims can be made.

No later contribution can enter that finalized set.

### 4. Prove membership

The participant chooses a destination wallet and generates a zero-knowledge membership proof.

The proof demonstrates that an eligible commitment belongs to the finalized sale set without revealing which specific commitment is being redeemed.

The claim is also bound to:

* The sale
* The allocation
* The destination
* A one-time nullifier

### 5. Claim

Venuo verifies the proof and settles the allocation to the selected destination.

The entitlement is marked as spent, preventing it from being claimed twice.

---

## What Venuo protects

Venuo is designed to protect the **contribution-to-claim mapping**.

The protocol can verify that a valid entitlement exists without publishing which finalized commitment produced the claim.

Protected properties include:

* Which contribution commitment is redeemed
* The direct contribution-to-claim relationship
* Claim redirection through a copied proof
* Reuse of the same entitlement

---

## What Venuo does not hide

Venuo is **not an anonymity protocol** and does not hide the blockchain itself.

The following remain observable:

* Funding wallet
* Contribution amount
* Sale and timing
* Public commitment
* Claim destination
* Token allocation
* Network metadata
* RPC and relayer metadata
* Other information available through external correlation

Privacy therefore depends on the effective claim set and on participant behavior.

For example, funding the destination wallet directly from the contribution wallet can create an external link between the two.

See the full [Privacy Model](https://venuo.fun/docs/privacy) before participating.

---

## Cryptographic model

At the protocol level, each sale maintains an append-only commitment tree.

Venuo's v1 design uses:

* **Poseidon Merkle tree**
* Fixed tree depth of 20
* Finalized root after the contribution window closes
* **Groth16** membership proofs
* **BN254** proving system
* Sale-scoped nullifiers
* Cryptographic recipient binding

The Solana program independently checks the relevant sale state, finalized root, allocation, destination binding, and nullifier state before settlement.

A valid proof by itself is not sufficient to authorize an arbitrary settlement.

The complete protocol relation, account model, security properties, threat model, and evaluation methodology are specified in the [Technical Paper](https://venuo.fun/docs/whitepaper).

---

## Sale lifecycle

```text
┌───────────────┐
│  Sale Created │
└───────┬───────┘
        │
        ▼
┌────────────────┐
│  Contributions │
│     Open       │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Finalize Sale │
│  Freeze Root   │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ Claims /       │
│ Refunds Open   │
└───────┬────────┘
        │
        ├───────────────┐
        ▼               ▼
   Successful       Cancelled
       Sale            Sale
        │               │
        ▼               ▼
      Claim           Refund
```

A cancelled sale follows a refund path rather than a token-claim path.

The same one-time entitlement cannot be used for both outcomes.

---

## For participants

A participant should:

1. Review the sale terms.
2. Generate and securely store the claim secret.
3. Contribute from the funding wallet.
4. Wait for the sale to finalize.
5. Choose a suitable destination wallet.
6. Generate the membership proof.
7. Claim the allocation.

The claim secret controls the entitlement and should be treated as sensitive information.

A fresh destination should have no direct funding relationship with the contribution wallet if the participant wants to preserve the intended separation.

---

## For creators

Creators configure:

* Contribution asset
* Contribution lot size
* Allocation per lot
* Sale capacity
* Contribution window
* Claim window
* Cancellation conditions

Before the sale opens, the creator funds the required token inventory.

After the sale closes:

* A successful sale enables claims and creator proceeds.
* A cancelled sale enables refunds.

Venuo handles allocation mechanics; token quality, creator credibility, liquidity, and downstream market risk remain separate considerations.

---

## Venuo is not a DEX

Venuo is not:

* A private DEX
* A privacy coin
* An anonymous trading protocol
* A continuous trading venue
* A bonding-curve launchpad

Venuo is a **scheduled primary-sale allocation layer**.

Its purpose is to separate the contribution phase of a token sale from the later allocation claim.

---

## Design principles

### Fixed rules

Sale terms and allocation rules are defined before participation.

### Deterministic allocation

Each valid contribution corresponds to a defined entitlement.

### Public settlement

The blockchain can verify the sale and settlement state.

### Private allocation mapping

The claim does not directly reveal which contribution commitment it redeems.

### One-time settlement

A nullifier prevents an entitlement from being reused.

### Destination choice

The allocation can be delivered to a destination other than the funding wallet.

### Explicit privacy boundaries

Venuo does not claim to hide information that remains publicly observable.

---

## Security & privacy considerations

Venuo's privacy guarantees are intentionally narrow.

The protocol is designed to remove one specific public relationship:

> **Which contribution commitment produced a particular claim.**

It does not guarantee:

* Anonymity
* Untraceability
* Network-level privacy
* Hidden contribution amounts
* Hidden claim destinations
* Hidden transaction timing
* Token price appreciation
* Liquidity
* Returns

External information can still create correlations.

Participants should understand the privacy model before using the protocol.

---
## Technical documentation

### Documentation

* [Overview](https://venuo.fun/docs)
* [The Mechanism](https://venuo.fun/docs/mechanism)
* [Participant Flow](https://venuo.fun/docs/flow)
* [Privacy & Risks](https://venuo.fun/docs/privacy)
* [Technical Paper](https://venuo.fun/docs/whitepaper)
* [Glossary](https://venuo.fun/docs/glossary)
* [$VENUO Tokenomics](https://venuo.fun/docs/tokenomics)

### Product

* [Venuo](https://venuo.fun)
* [Campaigns](https://venuo.fun/campaigns)

---

## Terminology

| Term                       | Meaning                                                                               |
| -------------------------- | ------------------------------------------------------------------------------------- |
| **Allocation**             | Fixed number of sale tokens attached to one valid contribution lot                    |
| **Claim**                  | One-time action that delivers an allocation to a proof-bound destination              |
| **Claim secret**           | Private entropy controlling an entitlement                                            |
| **Commitment**             | Public cryptographic value derived from the claim secret and sale context             |
| **Contribution lot**       | Fixed payment amount required for one entitlement                                     |
| **Entitlement**            | Right created by an accepted contribution to claim tokens or a refund                 |
| **Final root**             | Commitment-tree root frozen when contributions close                                  |
| **Fresh destination**      | Claim wallet without a direct funding link to the contribution wallet                 |
| **Membership proof**       | Zero-knowledge proof that an undisclosed commitment belongs to the finalized sale set |
| **Nullifier**              | One-time identifier preventing an entitlement from being reused                       |
| **Private allocation**     | Allocation delivery without directly publishing the redeemed contribution commitment  |
| **Recipient binding**      | Cryptographic binding between a claim and its exact destination                       |
| **Scheduled primary sale** | Token distribution with separate contribution and claim windows                       |

---

## Status

Venuo's privacy protocol is a **pre-product protocol design**.

The technical paper defines the v1 architecture and its intended security properties. Implementation, deployment, testing, audits, and production readiness should be evaluated independently as the project progresses.

---

## Disclaimer

Venuo does not guarantee token value, liquidity, returns, anonymity, or protection from all forms of blockchain correlation.

Participation in token sales involves substantial risk.

Always verify official sale information, token addresses, network details, and contract state through official Venuo resources before interacting with a sale.

---

## Links

**Website:** https://venuo.fun
**Docs:** https://venuo.fun/docs
**Technical Paper:** https://venuo.fun/docs/whitepaper
**X:** https://x.com/venuodotfun
**Telegram:** https://t.me/VenuoDotFun

---

<p align="center">
  <strong>Venuo</strong><br>
  Scheduled primary token sales on Solana.<br>
  Public contributions · Private allocation claims
</p>
