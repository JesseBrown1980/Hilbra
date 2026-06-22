# Hilbra JOIN handshake (DRAFT)

How a fabric with **zero prior relationship** becomes a discoverable PUBLIC member without any central authority issuing it a key.

## Steps

1. **Self‑mint (local, no network).** The newcomer generates an `ed25519` keypair — the **private half never leaves the host** (OS keystore/TPM). It derives `colony_pid = sha256(pubkey || name)[:16]` and binds a **named, consenting human owner‑PID** (accountability, mirroring how acer↔liris each have a human owner).
2. **Conformance‑before‑trust.** The newcomer runs the shipped harness: proves its `policy-oracle/pii-fragments.txt` is byte‑identical to canon (sha256 match) and its public‑tier responses leak zero PII. It emits a **signed** attestation `{colony_pid, policy_oracle_sha256, harness_result, ts}`.
3. **Register at PUBLIC.** It appends a federation‑ledger row at level 0 and adds itself to the signed registrar mirror `{colony_pid, public_key, public_endpoint, tiers_offered, conformance_hash, vouches[]}`.
4. **Challenge‑response with an existing peer** → mutual verify → the peer appends a `PEER-VOUCH` row to the cosign chain.
5. **Probation.** The newcomer is PUBLIC‑only and rate‑limited until it accrues ≥ K peer‑vouches (K rises with network size). It is discoverable by name immediately.

## What is automated vs gated

- **Automated:** self‑mint (1), conformance (2), registration (3), discovery (5).
- **Hard human/quorum gates (never automated):** operator‑class genesis of a *new owner*; promotion past PUBLIC to RESTRICTED+ (operator‑or‑quorum cosign); 3‑of‑3 sister cosign for sensitive grants. Auto‑admission stays **off**.

## What cloning this repo gives you

A **name and a PUBLIC view** — never write, never deep‑read. Deep tiers are unlocked only by an owner's explicit, out‑of‑band, scoped, revocable capability key. The registrar is a *directory*, not a CA: it lists identities and vouches; it issues no capability keys and holds no master secret.
