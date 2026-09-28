# Architecture Reference

This document describes the high-level architecture of Fable and design patterns used across the codebase.

## Overview

Fable is a distributed infrastructure orchestration platform organized as a Rust workspace with 11 specialized crates divided into libraries and services.

## Crate Classification

### Libraries (no binaries)

- **fable-common** — Shared utilities, error types, and common data structures used throughout the platform
- **fable-proto** — Protocol buffers (tonic/gRPC), serialization, and wire protocols for service communication
- **fable-raft** — OpenRaft state machine integration and consensus logic for distributed coordination
- **fable-kvm** — Virtual machine primitives and management APIs for hypervisor operations
- **fable-network** — Network abstractions, connectivity, and protocol implementations for peer communication
- **fable-storage** — Distributed storage layer and persistence abstractions for state durability

### Services (standalone binaries)

- **fable-api** — REST/gRPC API server; primary entry point for external clients and control plane
- **fable-idm** — Identity and access management service (authentication, authorization, policy enforcement)
- **fable-agent** — Node agent daemon; handles local state management and peer-to-peer communication
- **fable-scheduler** — Workload scheduling and job orchestration service for task distribution
- **fable-reconciler** — Control loop service ensuring actual infrastructure state matches desired state

## Architecture Patterns

### Layered Design

1. **Libraries provide domain logic** — Each library encapsulates a specific domain:
   - `fable-raft` abstracts consensus mechanisms
   - `fable-proto` defines wire formats and message schemas
   - `fable-common` centralizes shared types and utilities
   - `fable-storage` abstracts persistence concerns
   - `fable-network` handles networking primitives

2. **Services consume libraries** — Each service:
   - Depends only on libraries, never on other services
   - Uses `fable-proto` for communication protocols
   - Uses `fable-common` for shared types and error handling

3. **Cross-service communication** — Services communicate via:
   - `fable-proto` message types
   - gRPC/tonic for efficient, typed communication
   - No direct code dependencies between services

4. **No inter-service coupling** — Strict rule:
   - Services import libraries only
   - Services never import other services
   - Use message-based communication for all inter-service interactions
   - This prevents tight coupling and enables independent scaling/deployment

### Dependency Guidelines

When adding dependencies:

- **Libraries can depend on other libraries** — Establish a clear hierarchy (e.g., `fable-raft` depends on `fable-common`)
- **Services depend on libraries** — All service needs should come from libraries or external crates
- **Never create service→service dependencies** — Use message-based communication via `fable-proto` instead
- **Shared types should live in `fable-common`** — Avoid duplication and circular dependencies
- **Centralize domain logic in libraries** — Keep services thin and focused on runtime concerns

## Workspace Structure

```
crates/
├── fable-common/        # Shared types, errors, utilities (library)
├── fable-proto/         # Protocol buffers & wire formats (library)
├── fable-raft/          # Consensus logic (library)
├── fable-kvm/           # VM primitives (library)
├── fable-network/       # Network abstractions (library)
├── fable-storage/       # Storage layer (library)
├── fable-api/           # REST/gRPC API server (service binary)
├── fable-idm/           # Identity & access (service binary)
├── fable-agent/         # Node agent (service binary)
├── fable-scheduler/     # Job scheduler (service binary)
└── fable-reconciler/    # Control loop (service binary)
```

Each crate has its own:
- `Cargo.toml` — Package metadata and dependencies (inherits workspace settings)
- `src/lib.rs` — For libraries and service library code
- `src/main.rs` — For services only, the binary entry point

## Common Development Patterns

### Adding a New Module

1. Create `src/<module_name>.rs` in the appropriate crate
2. Declare it in `src/lib.rs`:
   ```rust
   pub mod module_name;
   ```
3. Export public types and functions as needed:
   ```rust
   pub use module_name::{Type, function};
   ```

### Cross-Crate Imports

```rust
use fable_common::{Error, Result};
use fable_proto::messages::{Request, Response};
use fable_raft::RaftState;
```

### Testing Patterns

- **Unit tests** — Co-locate with implementation in same file using `#[cfg(test)]` modules
- **Integration tests** — Place in `tests/` directory for testing crate interactions
- **Single crate iteration** — Run `cargo test -p <crate>` to iterate quickly on a single crate

### Service Implementation

Each service binary:
1. Imports libraries it needs
2. Implements runtime initialization in `main()` or startup module
3. Listens on gRPC/HTTP ports defined via configuration
4. Communicates with other services via message types from `fable-proto`

## Development Notes

- **Rust Edition**: 2021 (latest stable features)
- **MSRV** (if applicable): Check `Cargo.toml` for `rust-version` field
- **Release optimizations**: 
  - `opt-level = 3` — Maximum optimization
  - `lto = true` — Link-time optimization for better runtime performance
  - `codegen-units = 1` — Single codegen unit for best optimization (slower compilation)
- **Workspace resolver**: Version 2 for improved dependency resolution
- **Workspace-inherited metadata**: All crates inherit `version`, `edition`, `authors`, `license` from root `Cargo.toml`
