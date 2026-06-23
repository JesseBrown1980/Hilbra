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

> **Classifier strictness note — defense‑in‑depth, NOT an L0 leak (2026‑06‑23, acer + liris):** the "Why path‑fragments are the workhorse" note above describes how the live engines actually run — they apply the 45 to the **path**, plus a *separate* 8‑entry **content** list (only **3** of which — `cnpj`, `paypal`, `zelle` — are exact canon fragments; verified against `serve-recall.cjs` bytes). **This cannot leak to L0.** Classification is `is_pii(path|content) → L9; else public_canon(path) → L0; else → L5`, so a content‑only‑PII row with a neutral path falls to **L5 (federation — key‑required)**, never L0 (L0 serves only public‑canon paths). L0 is **MEASURED provably PII‑free** (`bank`/`vault`/`.pem`/`legal` probes level‑filtered to 0 on the 591k corpus). The only effect of the path‑vs‑content difference is whether a content‑only‑PII row sits at **L5 vs L9 — both consented, keyed tiers** — and the operator‑authorized acer↔liris key sharing intentionally permits that cross‑visibility (it is *not* a leak). Applying the full 45 to content too remains an **optional** strictness improvement (would move some L5 rows to L9); the canon contract is path **and** content, but this is hardening, **not** a release blocker.
