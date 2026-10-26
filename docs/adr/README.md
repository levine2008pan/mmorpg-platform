# Architecture Decision Records

This folder keeps the architecture decisions behind the MMORPG case study in the form of
**ADRs (Architecture Decision Records)** — short documents that capture *why* a decision was
made, not just what the system looks like today.

> **Note on provenance.** The original decision records were internal. The entries below are a
> reconstruction of the engineering approach described in the case study, written in the standard
> ADR format as a portfolio example. No confidential implementation detail is included.

## Why ADRs

An MMO accumulates decisions faster than a team can remember them. An ADR answers the question a
new engineer actually asks — *"why is it done this way?"* — without requiring the person who
decided it to still be on the team.

A good ADR is:

- **One decision** per record, in a few paragraphs;
- **Immutable** once accepted — a new decision supersedes an old one, the old one stays;
- **Explicit about trade-offs** — what got better, what got worse.

## Status values

| Status | Meaning |
| --- | --- |
| `Proposed` | Under discussion, not binding yet |
| `Accepted` | Agreed and in force |
| `Superseded by ADR-NNNN` | Replaced by a later decision |
| `Deprecated` | Still present, but should not be used for new work |

## Index

| ADR | Decision | Status |
| --- | --- | --- |
| [0001](0001-server-authoritative-model.md) | Server-authoritative gameplay model | Accepted |
| [0002](0002-scene-split-and-backend-services.md) | Split scene simulation from non-real-time backend services | Accepted |
| [0003](0003-language-boundaries-cpp-server-go-services.md) | C++ for game servers, Go for microservices | Accepted |

## Template

```markdown
# ADR NNNN — <short decision title>

- **Status:** Proposed | Accepted | Superseded by ADR-NNNN
- **Date:** YYYY-MM-DD
- **Deciders:** <roles, not names>

## Context

What situation forces a decision? Constraints, forces, requirements — and what happens
if nothing is decided.

## Decision

The decision, in one or two sentences, stated in the active voice:
"We will ...".

## Consequences

**Positive** — what becomes easier.
**Negative** — what becomes harder; the cost we accept.
**Neutral** — what changes without being clearly better or worse.

## Alternatives considered

| Option | Why not chosen |
| --- | --- |
| ... | ... |

## Notes

Links to the design doc, the case study section, or a follow-up ADR.
```

## File naming

```
docs/adr/0001-server-authoritative-model.md
docs/adr/0002-scene-split-and-backend-services.md
```

Four-digit zero-padded prefix, kebab-case title, one file per decision.
