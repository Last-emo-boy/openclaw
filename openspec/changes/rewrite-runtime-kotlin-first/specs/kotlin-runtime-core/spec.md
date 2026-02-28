## ADDED Requirements

### Requirement: Kotlin runtime hosts the core agent loop
The system SHALL run the primary OpenClaw runtime loop in Kotlin and MUST not require a Node process as the default host on Android.

#### Scenario: Start agent session on Android
- **WHEN** a new agent session is started on Android
- **THEN** the runtime host is the Kotlin runtime-core loop

### Requirement: Runtime loop uses explicit lifecycle states
The runtime loop MUST model agent execution using explicit lifecycle states (for example: initialized, running, waiting, completed, failed) and SHALL persist state transitions for recovery.

#### Scenario: Recover from process restart
- **WHEN** the app process is restarted during an active session
- **THEN** the runtime restores the latest persisted lifecycle state and resumes from the correct transition point

### Requirement: Session and memory are persisted and queryable
The runtime SHALL persist session events and derived memory records with stable identifiers so that context can be reconstructed for subsequent turns.

#### Scenario: Load prior context for next turn
- **WHEN** a follow-up turn references prior conversation state
- **THEN** the runtime reconstructs context from persisted session and memory records without requiring in-memory continuity
