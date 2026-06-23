# Policy Oracle — the provably‑PII‑free public tier

`pii-fragments.txt` is the **45‑fragment deny‑list** that makes Hilbra's public tier (level 0) safe to expose to strangers.

## How classification works (3 levels project onto the 6 fabric tiers)

```
assign_level(path, content):
    if is_pii(path, content):        return OWNER_PRIVATE (9)   # PII wins, checked FIRST
    if is_public_canon(path):        return PUBLIC (0)
    else:                            return FEDERATION (5)
```

- `is_pii` = the path or content contains any fragment in `pii-fragments.txt`, **or** a run of ≥14 consecutive digits (CNPJ/account/card‑like).
- Public tier serves **level 0 only**; federation (5) and owner‑private (9) require an authenticated, consented link.

## Why path‑fragments are the workhorse

When a fabric indexes *metadata* (paths) rather than full bodies, the content rules rarely fire, so classification rests on the path fragments — which is why the list must be **comprehensive**. This list was hardened by a full one‑pass audit over a real 591,286‑row corpus that initially leaked private keys (`.pem`/`.key`/`vault.master.key`), bank statements, and legal docs to the federation tier, and one bank README to public. After expansion: **0 PII in public, 0 high‑value gaps.**

## Conformance

A node proves conformance by:
1. shipping a **byte‑identical** copy of `pii-fragments.txt` (the harness pins its `sha256`),
2. **applying all 45 fragments to BOTH the path AND the content** of every row (not path‑only), plus the ≥14‑digit rule,
3. running the harness against its own corpus and showing its public‑tier responses match zero fragments,
4. emitting a **signed** conformance attestation `{colony_pid, policy_oracle_sha256, harness_result, ts}`.

Anyone can independently re‑run the harness against a member's public endpoint. A node that drifts from the canon hash is not conformant and gets no trust.

> **Known conformance gap (2026‑06‑23, found independently by acer + liris):** the "Why path‑fragments are the workhorse" note above describes how the live engines actually run today — they apply the 45 to the **path only**, plus a *separate* 8‑entry **content** list. Of those 8 content entries, only **3 are exact canon fragments** (`cnpj`, `paypal`, `zelle`); the rest of the 45 are matched against path only (verified against `serve-recall.cjs` bytes). That **under‑classifies**: a row whose *content* carries a canon fragment that isn't one of those 3 (e.g. `bank`, `legal`, `refund`) but whose path does not would not be caught, risking an L0 leak. The contract above (45 → path **and** content) is the canon; the engines (`serve-recall.cjs`, Rust `recall-serve` PR #8 — **HELD**) must be hardened to match it. Conservative direction only: hardening can only ever *raise* a row's privacy.
