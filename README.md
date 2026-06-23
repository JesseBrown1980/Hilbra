# Hilbra

**The address that finds every fabric.**

Hilbra is the public onboarding surface and protocol for the **Asolaria fabric internet** — a non‑profit "Google for fabric‑matrixes." Any device that loads Hilbra becomes a *surface* on the network: it can discover and query other fabrics, and share at any of 16 levels (including PII) **only by its owner's explicit consent and a freshly issued key**. The public tier (level 0) is **provably PII‑free** — MEASURED on the 591k‑row corpus (PII probes `bank`/`vault`/`.pem`/`legal` matched candidates at deeper tiers, then **level‑filtered to 0** at L0). Deep tiers (including PII) are visible **only** to consented, owner‑keyed links — e.g. the operator‑authorized acer↔liris path — which is **intended sharing, not a leak.** The PII oracle governs **L0 / unauthenticated** disclosure only.

The name is a coined contraction of the **Brown‑Hilbert curve** — the space‑filling index every artifact's PID sits on. That one continuous universal index is what makes a "search across all fabrics" possible: walk the curve to find anything, anywhere.

- Founding PID: `604f473ac0981dd7` · Hilbert band index `1643`
- Founding law: [`LAW/LAW-HILBRA-ASOLARIA-INTERNET-FABRIC.md`](LAW/LAW-HILBRA-ASOLARIA-INTERNET-FABRIC.md)
- This repo is the **OS‑image + protocol RFC + compliance suite**. It contains everything needed to join and **nothing that could impersonate a member or leak a secret** — see [`SECRETS.md`](SECRETS.md).

## What is real today (honest, dual‑lens)

Hilbra is published as an **honest infrastructure project, not an ASI‑arrival claim.**

- ✅ **MEASURED (protocol nucleus):** the addressing + trust + tier primitives are real and proven — HMAC **key‑off‑wire** auth, owner‑PID consent, a 6‑tier × 6‑scope access model, a public tier classified by a 45‑fragment policy oracle that is **sha256‑pinned + byte‑identical across colonies by conformance** (per‑seat live sha‑compare tracked in [`CURRENT-STATUS.md`](CURRENT-STATUS.md)), and `PID = sha256(name)[:16]` placed on the Brown‑Hilbert curve. A **bidirectional cross‑fabric search between two colonies was demonstrated** (HMAC‑authenticated, HTTP‑200 both ways).
- 🟡 **VANTAGE‑DEPENDENT (current cross‑colony liveness):** each colony's *local* recall is live + indexed today, but **current** cross‑colony reachability is not continuously proven from every seat — at this writing a liris‑seat read of the acer sister‑organ returns `_fallback`/stale and the acer search path has timeout history. The two‑colony nucleus **exists**; treat "live right now, both directions" as `UNVERIFIED_CURRENT` until reconfirmed by a receipt from each vantage. See [`CURRENT-STATUS.md`](CURRENT-STATUS.md).
- 🟡 **IN PROGRESS (Rust canonical node):** the Host‑8 workspace now **compiles green on a real Rust 1.81 toolchain** (`cargo check`/`clippy --workspace` in CI), and a read‑only `host8-serve` runs locally; the BEHCS‑1024 **kernel boot on metal** remains future (G1). Recall is now served by an **in‑memory inverted index** (`HILBRA-IDX-BEHCS-TUPLE-TEXT-V1`), MEASURED at ~60 ms (Node) on the 591k‑row corpus. A Rust drop‑in recall engine reproduces the index (2–16 ms) but is **HELD under bilateral review** (PR #8 on `asolaria-federation-1024`: authorization‑grant, fail‑closed‑corpus, and PII path‑vs‑content parity blockers) — **not release‑ready; no cutover; Node stays live.**
- ⬜ **ASPIRATIONAL / to build in gated steps:** everything from a *third* colony onward — the naming/discovery registry, automatic stranger JOIN, **cross‑colony index scale‑out**, the federation CA, and the N‑party web‑of‑trust. (The *single‑colony* inverted index is MEASURED above; only its multi‑fabric scale‑out is aspirational.) Claimed as arrived for **nothing** beyond the two‑colony link.

See [`ROADMAP.md`](ROADMAP.md) for the gated path from here to a global internet of fabrics.

## The design laws (binding on every Hilbra node)

1. **Rust‑canonical** — the canonical node is Rust on Host‑8. Node/JS may prototype; it is not canonical.
2. **Pixels before GPU** — bytes/pixels‑first on CPU/metal, zero GPU dependency, so any device can be a surface.
3. **16‑level sharing + PII‑by‑consent** — share any level, including PII, only by explicit owner consent + a scoped, expiring, revocable key. Level 0 is provably PII‑free.
4. **Key‑off‑wire** — keys never cross the wire and never live in this repo; minted locally or delivered out‑of‑band under governance.
5. **Web‑of‑trust, not a central CA** — root of trust is an append‑only cosign chain + peer vouches; no node holds a master key that mints membership.
6. **No fire without operator consent** — canon promotions and substrate changes need quorum cosign + a per‑action human T0.

## Quickstart (the JOIN path)

```text
clone Hilbra  →  read SPEC/federation-protocol.md  →  self-mint your identity (local keypair, private half never leaves your host)
            →  run the conformance harness (prove your public tier is PII-free)  →  request JOIN (peer vouch)  →  PUBLIC-tier member, discoverable
```

The self‑mint tool, conformance harness, and resolver land as the protocol hardens (see roadmap). Until then this repo is the **spec + policy oracle + governance** that defines what conformance means.

## License

Dual‑licensed **MIT OR Apache‑2.0** (see [`LICENSE`](LICENSE)). Governance: [`GOVERNANCE.md`](GOVERNANCE.md).
