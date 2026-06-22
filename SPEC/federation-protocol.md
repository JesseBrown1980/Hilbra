# Hilbra Federation Protocol v1 (DRAFT)

`spec_version = 1` · `alphabet = BEHCS-1024` · `tuple_dim = 60` · `policy_oracle = policy-oracle/pii-fragments.txt`

The wire + addressing + trust spec for the Asolaria fabric internet. Honest status: layers below marked **LIVE** are proven between two colonies; layers marked **DRAFT** are specified here and built in the gated roadmap.

## 1. Addressing (LIVE)

- **PID** = `sha256(canonical_name)`. Display key = first 16 hex chars; the **full sha256 is the durable collision‑proof join key** (16 hex = 64 bits has birthday‑collision risk at billions of artifacts, so the full hash is authoritative for merges).
- **Brown‑Hilbert placement**: each PID is placed on a Brown‑Hilbert space‑filling curve via the prime‑tower mapping `D_i ↦ p_i³`; the founding band is `892–1642` (apex registrations extend it). This single continuous index is what makes "search across all fabrics" a curve‑walk.
- **Mint rule**: `sha256_name_slice_16`. Reproducible: any node computes the same PID for the same canonical name.

## 2. Access tiers (LIVE)

Three serving levels project onto the fabric's native 6‑tier × 6‑scope model:

| level | name | who | redaction |
|---|---|---|---|
| 0 | PUBLIC | any agent (unauthenticated) | none — provably PII‑free |
| 5 | FEDERATION (≈RESTRICTED) | authenticated peer | hashes/summaries |
| 9 | OWNER‑PRIVATE (≈STEALTH/HIDDEN/SHADOW/SECRET) | owner‑trusted links only | existence/policy only |

Classification is by the [policy oracle](../policy-oracle/README.md); PII always wins over a public‑canon allow.

## 3. Trust transport — key‑off‑wire HMAC (LIVE)

Remote (non‑loopback) requests authenticate with HMAC‑SHA256 over a canonical message; **the key never crosses the wire.**

```
canonical = "LINK|" + owner_pid + "|" + host + "|" + verb + "|" + nonce + "|" + ts_unix_s_be64
HMAC      = HMAC_SHA256(shared_key, canonical)          # shared_key loaded from a local file, never transmitted
```

Headers: `X-Asolaria-Owner-PID`, `X-Asolaria-Colony`, `X-Asolaria-Verb`, `X-Asolaria-Nonce` (16‑byte hex), `X-Asolaria-TS` (unix seconds), `X-Asolaria-HMAC` (lowercase hex). The server recomputes with its own key copy; match ⇒ authenticated. A clock‑skew window + nonce defeat replay. **DRAFT hardening:** per‑message anti‑replay store, `ed25519`‑anchored key rotation bound to a ledger vantage row.

## 4. Consent + capability (LIVE owner‑PID consent; DRAFT capability keys)

- Every fabric is owned by a named **owner‑human PID**. The owner allow‑lists which owner‑PIDs may link and at what max level (a per‑owner grant).
- Deep tiers unlock **only** by explicit per‑owner consent + a freshly issued, scoped, expiring, revocable **capability key** delivered out‑of‑band (see [`SECRETS.md`](../SECRETS.md)). RESTRICTED needs operator‑or‑quorum cosign; STEALTH+ needs operator witness.

## 5. Endpoints (reference)

- `GET /api/public/search?q=…` — **unauthenticated**, hard‑clamped to level 0.
- `GET /api/search?q=…&level=…` — authenticated, level‑clamped to `min(requested, grant)`.
- `GET /api/search-all?q=…` — authenticated; scatter/gather across configured peers, merge + dedup by the full‑sha256 join key.
- `GET /api/health` — public node metadata (no row bodies).

See [`join-handshake.md`](join-handshake.md) for how a new fabric becomes a peer.
