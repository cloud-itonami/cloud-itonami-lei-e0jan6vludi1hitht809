# cloud-itonami-lei-e0jan6vludi1hitht809

> **Independent third-party archive/analysis. Not affiliated with, endorsed by, or sponsored by Chubb Limited.**

This repository archives the publicly published Terms of Use / Terms and Conditions of
**Chubb Limited**, with source-url and retrieval-date provenance, per
[ADR-2607110300](https://github.com/com-junkawasaki/root/blob/main/90-docs/adr/2607110300-cloud-itonami-lei-corporate-tos-catalog.md)
(`cloud-itonami-lei-corporate-tos-catalog`, `com-junkawasaki/root`). It is a read-only
reference/archive repository — it does not act, propose, or execute anything on the
company's behalf, and is not a governed Advisor/Governor actor.

## Company identity

- **Legal name**: Chubb Limited
- **LEI (ISO 17442)**: [E0JAN6VLUDI1HITHT809](https://search.gleif.org/#/record/E0JAN6VLUDI1HITHT809) (GLEIF-verified)
- **Jurisdiction**: CH
- **Website**: https://www.chubb.com
- **Ticker**: CB (NYSE)

## Contents

- `80-data/public/tos.journal.edn` — EDN quad-log of archived terms-of-service documents,
  each entry carrying `:tos/full-text`, `:tos/source-url`, `:tos/retrieved-at`,
  `:tos/sha256`, `:tos/doc-type`, and a `:tos/supersedes` chain for future revisions.
- `facts.edn` — 15 verified registry facts with per-fact provenance. **Generated** — see below.
- `scripts/verify-facts.cljs` — re-fetches every source `facts.edn` cites and fails if
  the live record disagrees. Vendored from `com-junkawasaki/root`
  (`scripts/lei-verify-facts.cljs`); fix issues in the canonical and re-vendor.
- `NOTICE` — copyright/attribution statement for the archived third-party text.
- `blueprint.edn` — machine-readable company identity record.

## Verifying the record

The LEI claims above used to be assertions with nothing in the repository behind
them. `facts.edn` now carries them as data, and every value in it was read out of
a public registry response whose URL and retrieval time sit next to the value:

```
nbb scripts/verify-facts.cljs           # check the recorded facts against the live sources
nbb scripts/verify-facts.cljs --write   # re-fetch and rewrite facts.edn
```

Eleven GLEIF/ISO URLs were fetched and fifteen facts recorded — the LEI record
(entity **ACTIVE**, registration **ISSUED**, `FULLY_CORROBORATED` / `CONFORMING`;
entity status and registration status are different fields and are recorded
separately; legal and headquarters address both `Barengasse 32, 8001, Zurich`
as GLEIF spells it),
its single ISIN `CH0044328745` (counted from `meta.pagination.total` *and*
mirrored as its own entity, because the whole list fits in one page), its
managing LOU and LEI-issuer accreditation (Bloomberg Finance L.P.), registration
authority `RA000548` (the UID-Register kept by the Federal Statistical Office,
entry `CHE-114.425.464`), ISO 20275 legal form `MVII` (Swiss company limited by
shares), reporting exceptions at both consolidation levels (`NATURAL_PERSONS` —
GLEIF's code for an entity controlled by natural persons with no intermediate
legal entity, so there is no parent to report), and **five direct children** each recorded as its own entity: Chubb
Group Holdings Inc., Chubb INA Holdings LLC, Chubb Group Management and Holdings
Ltd. (BM), Insurance Company of North America, and ACE American Insurance
Company. Nine of the eleven URLs answered `200` when the file was written; the
`direct-parent` and `ultimate-parent` endpoints answered `404` because GLEIF
publishes the exception side of that pair for this entity, which the checker
treats as a fact rather than a failure.

One thing this entity taught the generator: GLEIF lists `RA000548` under **two**
jurisdictions, Liechtenstein and Switzerland, in that order. The checker used to
read the first one, which would have recorded a Swiss company's register as
Liechtenstein's — a value the live response really contains and that no reader
would have known to doubt. `facts.edn` now records all of a register's
jurisdictions as parallel vectors (`:authority/country-codes ["LI" "CH"]`) and
says in `:source/note` that the entity's own jurisdiction is
`:company/jurisdiction` on the LEI record (`CH`), not a position in that list.

The checker's exit codes are three, not two: `0` every recorded fact matches the
live sources, `1` a citation broke or a fact drifted, `3` the check could not be
performed at all — an absent `facts.edn`, or every request failing at the
transport level — because a machine with no egress must not be able to publish a
green check. Each direction was exercised against the live API before this
landed: one edited value (`:authority/country-codes` `["LI" "CH"]` → `["CH"]`)
failed with exit 1 naming the entity and key, one deleted entity failed with exit
1 naming it as `ADDED`, and a DNS cut returned exit 3.

## Design rationale

See ADR-2607110300 in `com-junkawasaki/root` (`90-docs/adr/`) for why this repo exists,
why it is keyed by LEI rather than GTIN or ticker, and why full-text archival (with
provenance) was chosen over excerpt-only storage.
