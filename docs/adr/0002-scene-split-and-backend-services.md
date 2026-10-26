# ADR 0002 — Split scene simulation from non-real-time backend services

- **Status:** Accepted
- **Date:** 2020 (reconstructed)
- **Deciders:** Technical lead, backend lead

## Context

An MMO carries two very different kinds of traffic:

- **Real-time simulation** — combat, movement, AI, scene state. Tick-based, latency-sensitive,
  CPU-bound, and it must not pause for a database call.
- **Business operations** — account, inventory, mail, social, ranking, payments. Request/response,
  bursty, and expected to change (and be redeployed) far more often than combat rules.

Putting both in one process forces one scaling policy, one release cadence and one failure mode
onto workloads that have nothing in common.

## Decision

**Separate the simulation path from the business path.**

- **Scene servers (C++)** own the simulation for their scene or instance. They are authoritative,
  memory-resident, and never block on synchronous business calls.
- **Gateway** terminates client connections, authenticates, and routes to the correct scene.
- **Backend services (Go microservices)** own account, social and economy operations, with their
  own storage and deployment cycle.
- Cross-boundary effects are **asynchronous** where the player does not need a synchronous answer
  (mail, rankings, economy updates); everything the player waits on stays inside the simulation
  path.

## Consequences

**Positive**

- Scene servers scale horizontally with population without dragging the whole backend along.
- Business services can ship independently and restart without dropping combat.
- Failure is contained: a mail service outage degrades mail, not the whole game.
- Each side gets an appropriate language and toolchain (see [ADR 0003](0003-language-boundaries-cpp-server-go-services.md)).

**Negative**

- Distributed state: anything spanning scene and service needs an explicit consistency story
  (outbox/retry, idempotent handlers).
- More deployment units, more observability required — logs and tracing must be in place from the
  first version, not retrofitted.
- The gateway becomes a critical hop; it must stay thin and highly available.

**Neutral**

- Team structure follows the split: simulation engineers and service engineers with a shared
  contract, not a shared codebase.

## Alternatives considered

| Option | Why not chosen |
| --- | --- |
| One monolithic game server | Simplest to start, but forces whole-world scaling and couples release cadence |
| Entire world in one simulation process | Cannot scale past one machine's CPU, and a crash takes the whole world down |
| Combat as a microservice | An extra network hop in the hottest path; latency budget does not allow it |
| Sync RPC for every business call | Turns a database hiccup inside a business service into frame stalls in combat |

## Notes

The boundary rule that kept this workable in practice: **the simulation never waits on a business
service; it publishes intent and the service confirms asynchronously.**
