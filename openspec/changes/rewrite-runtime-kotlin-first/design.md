## Context

OpenClaw currently assumes a TypeScript/Node-hosted runtime where core agent behavior and tool execution share the same host model as skills/plugins. This creates friction on Android for lifecycle handling, background reliability, storage consistency, and permission hardening. The change introduces a Kotlin-first runtime with Android-native system integration while preserving workspace and skill contracts so existing usage patterns remain portable.

Primary constraints:
- Android runtime must respect OS lifecycle and background execution limits.
- Tool execution must move from implicit process access to explicit capability grants.
- Existing workspace/skills conventions should remain valid to reduce migration cost.
- TypeScript ecosystem support should remain possible through a bounded interoperability bridge.

Stakeholders:
- Android runtime users who need reliability and native integration.
- Skill/plugin developers who need a clear compatibility path.
- Maintainers responsible for security posture and long-term runtime maintenance.

## Goals / Non-Goals

**Goals:**
- Rebuild core runtime responsibilities in Kotlin: agent loop, session/memory, workspace coordination, tool routing, skill loading, and permission checks.
- Define an explicit capability model for tool execution with auditable policy decisions.
- Integrate Android-native components (Room, Proto, WorkManager, ForegroundService, notifications, shortcuts, share surface) into runtime operation.
- Support TS skills as optional remote/plugged-in execution targets via a formal bridge contract.
- Keep workspace and skills protocol compatibility where behavior is unchanged.

**Non-Goals:**
- Full one-to-one runtime behavior parity for every legacy Node execution edge case in the first release.
- Immediate migration of every existing TS skill to Kotlin.
- Deprecating all TS tooling; TS remains supported through bridge/plugin boundaries.
- Introducing unrestricted shell execution as a fallback path.

## Decisions

### 1. Runtime architecture split: `runtime-core` + `runtime-android`

Decision:
- Implement a Kotlin `runtime-core` for platform-independent orchestration and a `runtime-android` module for Android integrations.

Rationale:
- Keeps agent logic testable and portable.
- Prevents Android framework concerns from leaking into core orchestration.

Alternatives considered:
- Single Android-only module: simpler initial wiring, but poorer testability and higher coupling.
- Continue Node core with Android wrappers: lower short-term cost, but does not solve lifecycle/security constraints.

### 2. Event-sourced sessions with dual persistence (Room + Proto)

Decision:
- Persist conversation/session events and tool traces in Room.
- Persist runtime configuration, policy snapshots, and lightweight state in Proto schemas.

Rationale:
- Room supports queryability for session/memory reconstruction.
- Proto provides stable typed config/state with low overhead.

Alternatives considered:
- Room-only: workable but less ergonomic for versioned config snapshots.
- Proto-only: weaker querying for analytics/history reconstruction.

### 3. Capability-first tool routing and execution policy

Decision:
- Every tool declares required capabilities.
- Invocation path is `request -> policy evaluation -> grant check -> routed executor`.
- Default-deny policy for undeclared or ungranted capabilities.

Rationale:
- Enforces explicit privilege boundaries and prevents incidental broad host access.
- Makes permission decisions observable and testable.

Alternatives considered:
- Allowlist by tool name only: easier but too coarse for least-privilege enforcement.
- Runtime prompts without static declarations: flexible but inconsistent and difficult to audit.

### 4. Skill host abstraction with TS bridge as optional remote runtime

Decision:
- Introduce a skill host interface with multiple providers:
  - Native Kotlin skill host.
  - TS remote bridge host (for existing TS skills/plugins).

Rationale:
- Preserves ecosystem compatibility without coupling core runtime to Node.
- Allows phased migration of skills while maintaining a stable contract.

Alternatives considered:
- Hard cut to Kotlin-only skills: strongest simplification but unacceptable ecosystem breakage.
- Keep in-process TS host: weak isolation and reintroduces current security/lifecycle issues.

### 5. Android execution model: foreground interactive + scheduled background

Decision:
- Use ForegroundService for active interactive agent sessions.
- Use WorkManager for deferred/non-interactive jobs and retries.

Rationale:
- Aligns with Android constraints and improves execution reliability under app lifecycle pressure.

Alternatives considered:
- Service-only model: limited retry/resilience for deferred tasks.
- WorkManager-only model: insufficient for long-running interactive sessions.

## Risks / Trade-offs

- [Bridge complexity] TS interop boundary may become a bottleneck or failure point -> Mitigation: define strict protocol versioning, contract tests, and graceful fallback when bridge unavailable.
- [Migration risk] Existing skills may depend on implicit Node behaviors -> Mitigation: compatibility matrix, shim docs, and staged rollout with telemetry.
- [Performance overhead] Capability checks and routing add latency -> Mitigation: cached policy evaluation and precompiled capability maps.
- [Operational complexity] Two runtime ecosystems (Kotlin + TS bridge) increase support surface -> Mitigation: clear ownership boundaries and deprecation roadmap for low-value legacy paths.
- [Data migration integrity] Session format changes may corrupt historical continuity -> Mitigation: idempotent migrations, snapshot backup, and rollback path.

## Migration Plan

1. Build Kotlin runtime skeleton behind a feature flag with no behavior switch by default.
2. Implement core loop, session persistence, workspace handling, and capability policy engine.
3. Introduce tool routing adapters and enforce capability declarations for newly migrated tools.
4. Add TS bridge host and run compatibility tests against representative existing TS skills.
5. Integrate Android runtime wiring (services, workers, notifications, shortcuts, share surface).
6. Run dual-path validation in staging (legacy runtime vs Kotlin runtime) and collect correctness/perf telemetry.
7. Enable Kotlin runtime for selected cohorts, expand rollout progressively, and retain rollback switch.
8. Document migration guidance for skill authors and operators.

Rollback strategy:
- Keep runtime host selection configurable so deployments can revert to legacy host behavior while preserving session data.

## Open Questions

- Which transport should be canonical for TS bridge (local IPC, WebSocket, gRPC), and what minimum QoS guarantees are required?
- Should capability grants support per-session elevation, long-lived grants, or both?
- What is the minimum compatibility tier for legacy TS skills in the first GA milestone?
- How should memory/session schema evolution be versioned to support forward/backward compatibility?
