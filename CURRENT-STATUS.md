# Hilbra — current status (honest, vantage-tagged)

A single auditable snapshot of what is true **right now**, separated by the **vantage** it was
measured from. The README/ROADMAP describe the design; this file describes the live state and is
expected to drift — re-confirm by receipt, do not assume. Tags:

- `MEASURED_LIRIS` / `MEASURED_ACER` — observed live from that seat this session.
- `OPERATOR_OBSERVED_ACER` — observed by the operator on the acer machine.
- `MEASURED_GITHUB` — true of the repo bytes / CI on GitHub.
- `UNVERIFIED_CURRENT` — was demonstrated before, but is **not** continuously proven right now.

_Snapshot date: 2026-06-23 (bilateral acer + liris review)._

## Protocol nucleus

| Item | Status | Vantage |
|---|---|---|
| Addressing `PID = sha256(name)[:16]` on Brown‑Hilbert | real | CANON |
| HMAC key‑off‑wire auth (`LINK\|owner\|host\|verb\|nonce\|ts_be64`) | code path real | MEASURED_ACER (loopback only); remote cross‑colony interop `UNVERIFIED_CURRENT` |
| Owner‑PID consent + per‑owner grant `min(requested, grant)` | spec correct; **engine parity in progress** | CANON (spec); engine `UNVERIFIED` (PR #8 over‑grants) |
| 45‑fragment policy oracle byte‑identical across colonies | claimed; **this PR does NOT change the file** (hash preserved) | CANON; liris‑seat sha `MEASURED_LIRIS`; acer‑seat sha‑compare `UNVERIFIED_CURRENT` (receipt pending) |
| Inverted index `HILBRA-IDX-BEHCS-TUPLE-TEXT-V1` (~60 ms Node, 2.6M terms) | single‑colony | OPERATOR_OBSERVED_ACER (prior receipt) |

## Recall engines

| Engine | Status | Vantage |
|---|---|---|
| Node `serve-recall.cjs` linear (O(N) scan) | event‑loop‑stalls on 591k corpus — the "no Node" proof | MEASURED_ACER + MEASURED_LIRIS |
| Node `serve-recall.cjs` indexed, ~60 ms | the ~60 ms figure | OPERATOR_OBSERVED_ACER (prior receipt) — current acer `:4791` state not re‑confirmed this snapshot |
| Liris local recall `:4791` | live + indexed (health 200) | MEASURED_LIRIS |
| Rust `recall-serve` (PR #8) | terms/latency parity (2–16 ms) but **HELD** — see blockers | terms/latency MEASURED_ACER; CI green MEASURED_GITHUB; parity/disclosure `UNVERIFIED` |

## Current cross-colony liveness

| Claim | Status | Vantage |
|---|---|---|
| "bidirectional cross‑fabric search, live both directions, right now" | `UNVERIFIED_CURRENT` | liris‑seat read of acer sister‑organ is `_fallback`/stale; acer search has timeout history |
| Two‑colony nucleus *exists* (was demonstrated HTTP‑200 both ways) | true | MEASURED (prior receipts) |

## Open blockers (do not merge / no cutover until cleared)

PR #8 `recall-serve` (Rust), HELD under bilateral review — term‑count/latency parity is **tokenizer**
evidence only, **not** authorization or disclosure parity:

1. **Authorization over‑grant** — `verify_remote` returns owner‑private (L9) for every allow‑listed
   owner instead of the per‑owner grant; `/api/search?level=` is ignored. Must be `min(requested, grant)`.
2. **PII path‑vs‑content gap** — applies the 45 to path only + a separate 8 to content; canon requires
   all 45 on path **and** content (see `policy-oracle/README.md`). Affects both Rust and `serve-recall.cjs`.
3. **Fail‑open corpus handling** — empty/unreadable/truncated corpus still reports `ok:true`; unbounded
   HBI `len` allocation. Must fail **closed** + bound the read.
4. **Duplicate PID/BH ordering** — Node `Map.set` keeps the **last** duplicate; Rust `or_insert` keeps
   the **first** → divergent results.
5. **No corrupt/skipped counters; unbounded `thread::spawn`** per connection — add counters + bounded
   concurrency.

## Repo / CI honesty

| Item | Status | Vantage |
|---|---|---|
| `cargo check`/`clippy --workspace` on real Rust 1.81 | green | MEASURED_GITHUB (PR #7, #8 CI) |
| Secret‑scanner as an **enforced** merge gate | **not yet wired** — `.gitleaks.toml` present, workflow provided at `ci/secret-scan.yml`, operator must install at `.github/workflows/` (needs `workflow` scope) | MEASURED_GITHUB |
| `host8-serve` (read‑only) running | live locally | OPERATOR_OBSERVED_ACER |
| BEHCS‑1024 kernel boot on metal | not built (G1) | — |
