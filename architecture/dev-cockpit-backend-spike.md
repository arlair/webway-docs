# Dev Cockpit backend spike

Status: locked for the prototype; the production backend remains undecided.

## Decision

Build one SwiftUI application shell and compare two interchangeable supervisor
implementations behind a small `SupervisorClient` protocol:

1. A Swift helper registered with `SMAppService` and reached through XPC.
2. A Go daemon bundled by the app and reached through newline-delimited JSON over
   a Unix socket.

The SwiftUI views, application model and user-facing behaviour must be shared.
Only the client adapter and supervisor implementation should vary.

The supervisor is the background component that owns process lifecycle and log
collection. A service is a configured local site or process managed by the
supervisor.

## Prototype scope

- List configured services with running state and port.
- Start, stop and restart one service.
- Stream selectable, copyable logs without forcing the reader back to the end.
- Open a running service in the browser.
- Keep supervision working after the application window closes.
- Report a suspicious high-CPU child or orphan process.
- Install and start the supervisor at login.

Repository-owned service configuration must remain independent of the selected
backend so scripts and AI agents can inspect it.

## Excluded from the spike

- Todos and project planning.
- GitHub issue management.
- AI-agent orchestration.
- Charts and historical metrics.
- Liquid template execution.
- A production-ready migration from oxmgr.

## Evaluation

Choose the production backend using evidence from the spike:

- Start, stop and restart reliability.
- Helper installation, signing and update complexity.
- Streaming, reconnection and failure behaviour.
- Reuse between the daemon and CLI.
- Diagnostic quality.
- Idle CPU and memory use.
- Amount of platform-specific glue.
- Ease of testing and safely modifying the implementation.

Expected trade-off: Swift should have the simpler native macOS lifecycle; Go
should have the stronger reusable CLI, integration and cross-platform core. The
spike exists to measure whether the Go packaging and IPC boundary outweigh those
benefits.
