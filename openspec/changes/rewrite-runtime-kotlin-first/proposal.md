## Why

OpenClaw runtime behavior is currently centered on a TypeScript/Node execution model that is hard to align with Android-native lifecycle, storage, and permission boundaries. A Kotlin-first runtime enables tighter platform integration, stronger capability-based execution control, and clearer long-term maintainability for Android-first deployments.

## What Changes

- Build a new Kotlin-native OpenClaw runtime core that re-implements the agent loop, memory/session management, workspace orchestration, tool routing, skill loading, and permission enforcement.
- Introduce a capability-based execution system for tools so each tool invocation is explicitly authorized and bounded instead of relying on unrestricted process execution patterns.
- Integrate Android-native primitives for persistence and runtime operation: Room/Proto for storage, WorkManager/ForegroundService for background work, and platform permission/notification/share integrations.
- Define a TypeScript interoperability boundary where TS code runs as optional remote skills/plugins, not as the primary runtime host.
- Add compatibility rules for existing workspace/skills contracts, plus a bridge strategy for existing TS skills when feasible.
- **BREAKING**: The default runtime host model shifts from Node/TS-first to Kotlin-first on Android, and TS runtime execution becomes optional/bridged.

## Capabilities

### New Capabilities
- `kotlin-runtime-core`: Kotlin implementation of core runtime responsibilities (agent loop, session/memory, workspace, tool routing, skill loader, permissions).
- `android-native-runtime-integration`: Android-native lifecycle, storage, background execution, and user-surface integrations (notifications, shortcuts, share sheet).
- `ts-skill-bridge`: Interop contract that allows TS ecosystem features to run as remote skills/plugins with explicit boundaries and fallback behavior.
- `runtime-capability-security`: Capability model and policy checks for tool execution, including grant evaluation and auditability.

### Modified Capabilities
- None.

## Impact

- Affected code: new Kotlin runtime modules, Android app/runtime wiring, tool routing and permission layers, skill loader boundaries, and compatibility adapters.
- Affected APIs/contracts: runtime execution contract, tool invocation policy, skill host protocol, and workspace/session persistence interfaces.
- Dependencies/systems: AndroidX Room/WorkManager, ForegroundService flows, Proto schemas, and cross-language bridge components for TS skills.
- Operational impact: migration path from Node-hosted runtime behavior to Kotlin-hosted runtime behavior, including compatibility and rollout controls.
