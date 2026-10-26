# ADR 0003 — Language boundaries: C++ for game servers, Go for microservices

- **Status:** Accepted
- **Date:** 2020 (reconstructed)
- **Deciders:** Technical lead

## Context

The project needed a server language for two code bases with opposite priorities:

- **Simulation** — deterministic tick timing, memory control, no GC pauses during combat, mature
  threading and networking. Hiring pool: game server engineers.
- **Business services** — fast iteration, high concurrency for I/O-bound request handling, easy
  onboarding, simple deployment.

A single language for everything is easier to staff and review; two languages cost more in
tooling, build and shared libraries.

## Decision

**Use each language where its strengths land, with a hard boundary at the service edge.**

- **C++** for the authoritative game servers (simulation, scene, world logic) — chosen for latency
  determinism and memory control.
- **Go** for the non-real-time microservices (account, social, economy) — chosen for concurrency,
  deployment simplicity and onboarding speed.
- **Unity / C#** on the client, consuming the same protocol as any other client.
- Cross-language communication happens only through **versioned protocols** (message schemas), never
  through shared source code.

## Consequences

**Positive**

- Each code base can be reviewed and staffed by people who know that domain.
- Service iteration is fast: a Go service rebuilds and deploys in seconds.
- Simulation keeps predictable frame timing without fighting a runtime's garbage collector.
- Protocol-first boundary forces explicit contracts — a long-term benefit in an MMO.

**Negative**

- Two build systems, two dependency policies, two debugging toolchains.
- Protocol changes need coordination across both sides; schema discipline is mandatory.
- Harder to move an engineer between simulation and services without ramp-up time.

**Neutral**

- The hiring plan splits accordingly: C++ game-server engineers and Go service engineers, with a
  shared understanding of the message contracts.

## Alternatives considered

| Option | Why not chosen |
| --- | --- |
| C++ everywhere | Business services would need the same tooling and ramp-up as the simulation code, slowing feature iteration |
| Go everywhere | GC pauses in a tick loop are unacceptable for combat; memory control would be lost |
| C# everywhere (server + client) | Attractive for shared code, but the server-side runtime profile and hiring pool did not fit the simulation requirements |
| Rust | Strong fit technically, but the hiring pool and existing team expertise did not support it at the time |

## Notes

If the project were starting today with the same constraints, this decision would likely still hold
for the simulation side. The service side is the part worth re-evaluating — Go's trade-offs were
right for a small team shipping quickly, and that is the assumption to revisit first.
