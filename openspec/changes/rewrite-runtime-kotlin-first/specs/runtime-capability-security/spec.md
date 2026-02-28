## ADDED Requirements

### Requirement: Tools declare required capabilities
Each tool MUST declare a static set of required capabilities, and the runtime SHALL reject registration or execution of tools with missing capability declarations.

#### Scenario: Tool missing capability metadata
- **WHEN** a tool is loaded without required capability declarations
- **THEN** the runtime rejects the tool and reports a policy validation error

### Requirement: Capability policy enforces default deny
The runtime MUST evaluate capability grants before every tool invocation and SHALL deny invocations when the required capability set is not fully granted.

#### Scenario: Invocation without grant
- **WHEN** a tool invocation requests a capability that is not granted for the current session and identity
- **THEN** the runtime denies execution and returns a permission error without side effects

### Requirement: Capability scope boundaries are enforced
Granted capabilities MUST include scope boundaries (for example workspace path scope, network scope, or operation scope), and the runtime SHALL block operations that exceed granted scope.

#### Scenario: Tool attempts out-of-scope file access
- **WHEN** a tool with workspace-scoped file capability attempts access outside the allowed workspace scope
- **THEN** the runtime blocks the operation and records a scope violation

### Requirement: Policy decisions are auditable
The runtime SHALL persist an audit record for each capability decision, including requested capability set, decision outcome, and principal/session context.

#### Scenario: Inspect denied action history
- **WHEN** an operator reviews session security history
- **THEN** denied capability checks are available with decision context sufficient for troubleshooting
