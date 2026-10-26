# ADR 0001 — Server-authoritative gameplay model

- **Status:** Accepted
- **Date:** 2020 (reconstructed)
- **Deciders:** Technical lead, backend lead

## Context

The product is a persistent, large-scale multiplayer RPG. Players trade items, fight each other
and progress through content that other players can see. Two things follow from that:

1. **The client cannot be trusted.** It runs on the player's machine, can be modified, and can
   send any input. If the client decides outcomes, cheating becomes a feature-detection problem
   instead of a design property.
2. **State must be consistent across clients.** Two players looking at the same fight must agree
   on the result, even when their clients render at different frame rates.

The alternative is a client-authoritative model: the client simulates, the server persists and
broadcasts. That is far cheaper to build, and acceptable for co-op or single-player-first games —
but it does not survive an economy where items have value.

## Decision

**Gameplay is server-authoritative.** The server owns all state transitions — combat resolution,
loot, progression, economy. The client sends *input* and *intent*, receives *state*, and renders
an interpolated view. The client's simulation is a prediction for responsiveness, never the source
of truth.

## Consequences

**Positive**

- Cheating requires exploiting server logic, not editing local memory — a much smaller attack
  surface.
- Players see consistent outcomes; disputes are settled by server state.
- Server-side logic is testable without a rendering client.

**Negative**

- Every gameplay feature needs a server implementation and a client presentation — roughly double
  the work of a client-only prototype.
- Latency becomes a design constraint: input cannot wait for a round trip, so the client must
  predict and reconcile.
- The server carries the CPU cost of simulation and the bandwidth cost of state replication.

**Neutral**

- Client code becomes a view layer; UI and rendering can iterate faster than gameplay rules.

## Alternatives considered

| Option | Why not chosen |
| --- | --- |
| Client-authoritative + server validation | Validation of an untrusted simulation is fragile at scale and adversarial at the edges |
| Server-authoritative with no client prediction | Simple and correct, but movement and combat feel laggy under realistic latency |
| Peer-to-peer for combat | Cannot provide persistent state or a trustworthy economy |

## Notes

See [0002](0002-scene-split-and-backend-services.md) for how this model is distributed across
processes, and the case study's "Why this shape" section for the wider architecture.
