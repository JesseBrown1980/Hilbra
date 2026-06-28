# Hilbra — fabric-internet / atlas-recall / PTP-reflection bridge

Date: 2026-06-28 · Branch: `acer/p4b-hilbra-bridge` · **docs-first, E=0.** No runtime fire, no cutover, no corpus, no keys. For liris attack-verify before main.

## Claim
Hilbra is the **bridge across vantages** — the lane by which a single vantage reaches the rest of the
federation it *cannot see alone*:

> the **fabric-internet** comms lane + **atlas-recall** (cross-vantage memory reach) + the
> **PTP-reflection** path (prime-to-prime addressing reflected across acer / liris / falcon).

It is not a separate brain; it is the connective tissue that makes **distributed seeing** possible.

## How it maps to the root
- In the nested-agent root, **Agent 3 "asks the fabric via Hilbra / recall"** — Hilbra *is* that
  fabric-reflection lane. The watcher/self-reflection (Agent 2) is local; the **fabric-reflection**
  (Agent 3) reaches out through Hilbra.
- **atlas-recall:** Hilbra carries recall/atlas lookups *between* seats — e.g. liris atlas-recalling into
  the 8-byte host; how one vantage borrows another's memory.
- **PTP-reflection:** the measured prime-to-prime (`c·√p`) addressing, reflected/aligned across vantages,
  so the same 8-byte coordinate means the same thing on acer, liris, and falcon.
- This is the operational answer to *"no single AI sees it all"*: Hilbra is how the partial vantages
  compose. (Root atom unchanged — see HYPER-BECHS `ROOT-PRIMITIVE` + `MAP.md`.)

## Evidence tags / honest boundaries
- CANON: recall + Hilbra = the comms layer; "liris using the fabric-internet to atlas-recall into the
  8-byte host" is the cross-vantage reflection path.
- BOUNDARY / MEASURED-stale: the acer Hilbra instance (`:4790`) is **stale** per the live port map
  (`:4796` is the current recall engine). This repo is the **bridge spec/role**, not a claim that the
  live cross-host bus is up — the LAN bus (`:4944`/`:4947`) is intermittently down, which is *why*
  GitHub-as-mediator is the working bridge right now.
- BOUNDARY: cross-vantage comms is exactly the federation blind spot P1 named; Hilbra is the intended
  closer, not a proof it is currently closed.

## Hard holds (T0 only; NOT this docs pass)
No live cross-host bus bring-up, no USB-SOVLINUX enum, no 35TB ADC, no live census, no private-root scan,
no `:5088`/daemon redeploy.
