## 1. Runtime Foundation

- [ ] 1.1 Create Kotlin runtime module layout (`runtime-core`, `runtime-android`) and wire build configuration.
- [ ] 1.2 Define core runtime interfaces for agent loop, session store, memory store, workspace manager, tool router, and skill host.
- [ ] 1.3 Implement lifecycle state machine primitives for agent execution (`initialized`, `running`, `waiting`, `completed`, `failed`).
- [ ] 1.4 Add feature flag/runtime selection switch to gate Kotlin runtime rollout.

## 2. Session and Memory Persistence

- [ ] 2.1 Design Room entities/DAOs for session events, tool traces, and memory records with stable identifiers.
- [ ] 2.2 Define Proto schemas for runtime config, capability policy snapshots, and lightweight runtime state.
- [ ] 2.3 Implement persistence adapters that reconstruct runtime context from Room + Proto on startup/recovery.
- [ ] 2.4 Add migration scaffolding and backup/restore hooks for session schema evolution.

## 3. Capability Security and Tool Routing

- [ ] 3.1 Implement tool capability declaration schema and validation on tool registration.
- [ ] 3.2 Implement policy evaluator with default-deny behavior for missing or ungranted capabilities.
- [ ] 3.3 Implement scope enforcement checks (workspace path, network, operation scope) in routed executors.
- [ ] 3.4 Persist auditable capability decision logs for each invocation outcome.

## 4. Android Native Integration

- [ ] 4.1 Implement ForegroundService execution path for interactive runtime sessions.
- [ ] 4.2 Implement WorkManager workers for deferred tasks and retryable jobs.
- [ ] 4.3 Wire notification actions, launcher shortcuts, and share-sheet entry handling into runtime start flows.
- [ ] 4.4 Add lifecycle/resilience tests for background transitions and process restarts.

## 5. TS Skill Bridge Compatibility

- [ ] 5.1 Define skill host abstraction and provider registry for native and bridge-backed skill execution.
- [ ] 5.2 Implement TS bridge handshake with protocol version negotiation and compatibility rejection.
- [ ] 5.3 Implement bridge failure isolation so bridge crashes return structured errors without crashing runtime-core.
- [ ] 5.4 Create compatibility matrix tests for representative existing TS skills via bridge mode.

## 6. Rollout, Observability, and Documentation

- [ ] 6.1 Add telemetry/metrics for lifecycle transitions, capability denials, bridge failures, and recovery behavior.
- [ ] 6.2 Implement staged rollout controls and rollback path to legacy runtime host selection.
- [ ] 6.3 Document migration guidance for operators and skill authors, including capability mapping and bridge limits.
- [ ] 6.4 Run end-to-end validation for interactive sessions, deferred jobs, and share/shortcut workflows on Android.
