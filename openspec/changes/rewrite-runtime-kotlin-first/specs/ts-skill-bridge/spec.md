## ADDED Requirements

### Requirement: TS skills run through an optional bridge host
The system SHALL support TypeScript skills through an optional bridge host and MUST allow runtime operation without the bridge when only native skills/tools are required.

#### Scenario: Bridge not configured
- **WHEN** the runtime starts without TS bridge configuration
- **THEN** the runtime remains functional for Kotlin-native skills and tools

### Requirement: Bridge protocol compatibility negotiation
The system MUST perform protocol version negotiation before executing a TS skill and SHALL reject execution when no compatible protocol version exists.

#### Scenario: Version mismatch during handshake
- **WHEN** the TS bridge reports an unsupported protocol version
- **THEN** the runtime denies skill execution and records a compatibility error with remediation guidance

### Requirement: Bridge failure isolation and fallback behavior
The runtime MUST isolate bridge failures so they do not crash the core runtime loop and SHALL return a structured skill execution error to the caller.

#### Scenario: Bridge process crashes mid-execution
- **WHEN** the bridge host becomes unavailable during skill execution
- **THEN** the runtime marks the skill call as failed, keeps the session alive, and permits subsequent non-bridge operations
