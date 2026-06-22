# Hilbra Governance

Hilbra is a **non‑profit public good**, run with the discipline of a large software‑engineering org but owned by no company. Its root of trust is **not a corporate CA** — it is an append‑only cosign chain plus a web‑of‑trust mesh.

## Principles

- **No central authority.** No node (not even the founding colonies) holds a master key that can mint membership. A colony is real iff its row is in the cosign chain **and** it carries ≥ K valid peer‑vouches.
- **Owner sovereignty.** Every fabric belongs to a named, consenting human owner‑PID. Deep‑tier sharing is **never transitive** — always per‑owner consent.
- **No fire without consent.** Canon promotions and substrate changes require quorum cosign + a per‑action human T0. An emergency **OP‑VETO** can stop any action. Rollback never rewrites history — the row stays; a new row moves the binding.
- **Honesty is law.** Every claim carries a provenance grade (MEASURED / CANON / UNVERIFIED) with evidence. Silent truncation or unbacked "MEASURED" tags are forbidden.

## Trust lifecycle

`self-mint → conformance attestation → PUBLIC probation (rate-limited) → peer-vouches → PUBLIC-trusted`. Promotion past PUBLIC to RESTRICTED+ is a hard human/quorum gate. Revocation is a `REVOKE` cosign row that mirrored registrars honor (the CRL/OCSP analog).

## Contribution + safety gates

- All changes go through PRs; the CI **secret‑scanner is a hard merge gate** (`.github/workflows/ci.yml`) — see [`SECRETS.md`](SECRETS.md).
- The public PII policy oracle (`policy-oracle/pii-fragments.txt`) cannot silently drift — CI guards it, and conformance pins its sha256.
- Sensitive/structural changes require multiple cosigners (CODEOWNERS, to be added: the operator pair + designated maintainers).

## Identity of the founders

Hilbra was founded by the Asolaria federation operator pair (`OP-JESSE-PID`, `OP-RAYSSA-PID`) across two colonies. These are public owner identities; no private key, financial, or personal data is in this repository (see [`SECRETS.md`](SECRETS.md)).
