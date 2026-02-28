## ADDED Requirements

### Requirement: Android-native background execution model
The runtime MUST use ForegroundService for interactive long-running sessions and SHALL use WorkManager for deferred or retryable background jobs.

#### Scenario: Interactive session enters background
- **WHEN** an interactive agent session continues after the app is backgrounded
- **THEN** execution is maintained by ForegroundService with user-visible service notification

#### Scenario: Deferred task requires retry
- **WHEN** a non-interactive task fails due to transient conditions
- **THEN** WorkManager schedules a retry according to the configured backoff policy

### Requirement: Android-native storage integration
The runtime SHALL store relational session/tool data in Room and MUST store typed runtime configuration and lightweight policy state in Proto schemas.

#### Scenario: Persist a tool invocation record
- **WHEN** a tool invocation completes
- **THEN** invocation metadata and outcome are persisted in Room and relevant policy/config snapshots are persisted in Proto-backed state

### Requirement: User-surface integration for agent actions
The runtime SHALL expose Android-native notification actions and MUST support launcher shortcuts and share-sheet entry points for agent workflows.

#### Scenario: User invokes agent from share sheet
- **WHEN** the user shares content to OpenClaw from another app
- **THEN** the runtime receives the payload and starts a scoped agent workflow with that content as initial context
