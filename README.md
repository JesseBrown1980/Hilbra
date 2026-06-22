# Hilbra

**The address that finds every fabric.**

Hilbra is the public onboarding surface and protocol for the **Asolaria fabric internet** — a non‑profit "Google for fabric‑matrixes." Any device that loads Hilbra becomes a *surface* on the network: it can discover and query other fabrics, and share at any of 16 levels (including PII) **only by its owner's explicit consent and a freshly issued key**. The public tier (level 0) is provably PII‑free.

The name is a coined contraction of the **Brown‑Hilbert curve** — the space‑filling index every artifact's PID sits on. That one continuous universal index is what makes a "search across all fabrics" possible: walk the curve to find anything, anywhere.

- Founding PID: `604f473ac0981dd7` · Hilbert band index `1643`
- Founding law: [`LAW/LAW-HILBRA-ASOLARIA-INTERNET-FABRIC.md`](LAW/LAW-HILBRA-ASOLARIA-INTERNET-FABRIC.md)
- This repo is the **OS‑image + protocol RFC + compliance suite**. It contains everything needed to join and **nothing that could impersonate a member or leak a secret** — see [`SECRETS.md`](SECRETS.md).

## What is real today (honest, dual‑lens)

Hilbra is published as an **honest infrastructure project, not an ASI‑arrival claim.**

- ✅ **MEASURED / live:** a crypto‑keyed, consent‑gated, **bidirectional cross‑fabric search** between two colonies — proven HTTP‑200 both ways, HMAC key‑off‑wire, owner‑PID consent, a 6‑tier × 6‑scope access model, and a public tier proven PII‑free by a 45‑fragment policy oracle that is byte‑identical on both colonies. Addressing is `PID = sha256(name)[:16]` placed on the Brown‑Hilbert curve.
- 🟡 **SCAFFOLD / in progress:** the Rust **Host‑8** kernel (the canonical node) is built but not yet proven on a real toolchain — that's literally Gate‑0 of the roadmap.
- ⬜ **ASPIRATIONAL / to build in gated steps:** everything from a *third* colony onward — the naming/discovery registry, automatic stranger JOIN, a scaled inverted index, the federation CA, and the N‑party web‑of‑trust. Claimed as arrived for **nothing** beyond the two‑colony link.

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
