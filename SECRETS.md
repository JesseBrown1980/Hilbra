# SECRETS — read this first

**No keys, HMAC secrets, enrollment tokens, or capability tokens are in this repository. Ever.**

This repo is the equivalent of an OS install image: it does **not** contain your keys, any more than an OS ISO contains your password. Cloning Hilbra and self‑minting gives you a **name and a PUBLIC view only** — never write access, never deep‑read.

## Two strictly separated planes

**PUBLIC plane (this repo):** the spec, the policy oracle (a deny‑list of *patterns*, not data), the resolver, the conformance harness, the signed registrar mirror, and signed conformance attestations (which carry *proofs*, never data). Everything here is safe for anyone to fork.

**PRIVATE plane (NEVER this repo):**
- **Identity keys** — each node generates an `ed25519` keypair locally; the private half lives only in the host's OS keystore/TPM and **never leaves the machine** and **never enters this repo or the cleartext wire**.
- **Link/search keys (HMAC)** — shared symmetric secrets travel an **out‑of‑band** channel under owner/governance control (an operator‑issued enrollment token over an authenticated path, or in‑person verification). They are recorded only as `hashes_and_summaries_only` — existence provable, content never dumped.
- **Capability keys** — per‑grant, scoped `{grantee_pid, tier, scopes, expiry, revocable}`, issued out‑of‑band on explicit owner consent. Deep tiers (RESTRICTED+) require operator‑or‑quorum cosign.

## Why this is absolute

A single leaked HMAC key would let an attacker impersonate a colony. That is the #1 catastrophic failure mode, so the design forbids it absolutely:

- A **secret‑scanner** (gitleaks, config [`.gitleaks.toml`](.gitleaks.toml)) is the intended hard merge gate. The runnable workflow ships in this repo as [`ci/secret-scan.yml`](ci/secret-scan.yml); **installing it at `.github/workflows/` and marking it a required check is an operator step** (it needs a token with `workflow` scope) — tracked in the roadmap. Once active, any commit containing a key, token, private‑key file, or key‑bearing filename is **blocked**. *(Honest status as of 2026‑06‑23: the config is present; the workflow is provided but not yet installed as an enforced check.)*
- Identity is **decoupled from capability**: a name is cheap; trust is expensive (a named consenting human owner + a conformance attestation + a rising peer‑vouch threshold + rate‑limited public probation).

If you think you need to put a secret in this repo, you don't. Mint it locally, or move it out‑of‑band.
