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
| Inverted index `HILBRA-IDX-BEHCS-TUPLE-TEXT-V1` (Node): build 32.5 s, 591,286 rows, **2,614,638 terms**, 23,930,053 postings, 0 skipped; **~56–67 ms/query**; L0 PII probes (`bank`/`vault`/`.pem`/`legal`) → candidates 239/732/26/1885 then **level‑filtered to 0 → provably PII‑free** | concrete operator receipt (screenshots 2026‑06‑23); today's run measured even faster | OPERATOR_OBSERVED_ACER |

## Recall engines

| Engine | Status | Vantage |
|---|---|---|
| Node `serve-recall.cjs` linear (O(N) scan) | event‑loop‑stalls on the 591k **acer** corpus — the "no Node" proof | MEASURED_ACER (591k acer corpus); liris confirmed the same linear‑stall class on its own corpus → MEASURED_LIRIS |
| Node `serve-recall.cjs` indexed, ~60 ms | the ~60 ms figure | OPERATOR_OBSERVED_ACER (prior receipt) — current acer `:4791` state not re‑confirmed this snapshot |
| Liris local recall `:4791` | live + indexed (health 200) | MEASURED_LIRIS |
| Rust `recall-serve` (PR #8, `a8e837f`) | **MEASURED:** terms **2,614,638** (exact match to the Node receipt), 591,286 rows, build ~45 s, query **2–16 ms** (beats the ~56–67 ms bar), L0 probes `bank`/`vault`/`.pem`/`legal`/`password` → **0** (PII-free), thread-per-conn (no stall). Still **HELD** for robustness/parity hardening (fail-closed corpus, bounded alloc/concurrency, dup PID/BH ordering, honor requested level) — **not** a PII issue | perf + L0-PII-free MEASURED_ACER; CI green MEASURED_GITHUB; robustness/parity `UNVERIFIED` |

## Current cross-colony liveness

| Claim | Status | Vantage |
|---|---|---|
| "bidirectional cross‑fabric search, live both directions, right now" | `UNVERIFIED_CURRENT` | liris‑seat read of acer sister‑organ is `_fallback`/stale; acer search has timeout history |
| Two‑colony nucleus *exists* (was demonstrated HTTP‑200 both ways) | demonstrated by prior receipts; not continuously proven now | `UNVERIFIED_CURRENT` |

## Open blockers (do not merge / no cutover until cleared)

PR #8 `recall-serve` (Rust), HELD under bilateral review — term‑count/latency parity is **tokenizer**
evidence only, **not** authorization or disclosure parity:

1. **Authorization over‑grant** — `verify_remote` returns owner‑private (L9) for every allow‑listed
   owner instead of the per‑owner grant; `/api/search?level=` is ignored. Must be `min(requested, grant)`.
2. **PII path‑vs‑content — RESOLVED as non‑blocking (defense‑in‑depth, *not* a leak).** Engines apply
   the 45 to path + 8 to content. This does **not** leak to L0: the public tier is **MEASURED**
   PII‑free, and a content‑only‑PII row falls to **L5 (federation, key‑required)**, never L0. The
   operator‑authorized acer↔liris keyed PII visibility is **intended**, not a breach. Applying all 45
   to content is optional strictness (L5→L9) — see `policy-oracle/README.md`. *(Items 1, 3, 4, 5 are
   the real merge blockers.)*
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

## TO VERIFY (scale — NOT promoted)

Scale/telemetry claims are a real Asolaria subsystem but carry **no Hilbra receipt yet**, so they
get **no `MEASURED` tag and do not appear in any live table above**. Before any 1K/1M/1B swarm claim
is promoted into Hilbra status, it must be cross-checked against the named owning surfaces:

| Claim to verify | Required receipt | Status |
|---|---|---|
| Omnimets aggregation across the micro-agent swarm | Omnimets aggregation rows | `TO_VERIFY` |
| Scale-test fanout ladder 1K → 1M → 1B | scale-test runner receipts (a *ladder*, not live capacity) | `TO_VERIFY` |
| 4-worker Opencode scaffold (big-pickle / gpt5-nano / minimax-m2.5 / nemotron-3-super) | Opencode 4-worker scaffold logs (a load-test *harness*, not full-swarm runtime) | `TO_VERIFY` |
| Bilateral cosign cross-colony sync | bilateral cosign audit rows | `TO_VERIFY` |
