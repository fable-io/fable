# Fable

Fable is a distributed infrastructure orchestration platform built in Rust.

## Quick Start

See the [documentation](docs/README.md) for detailed guides:

- **[BUILD.md](docs/how-to/BUILD.md)** — Build, test, and development workflow
- **[ARCHITECTURE.md](docs/reference/ARCHITECTURE.md)** — System architecture and design patterns
- **[CLAUDE.md](CLAUDE.md)** — Quick reference for Claude Code

## Overview

Fable is organized as a Rust workspace with 11 specialized crates:

### Libraries
- **fable-common** — Shared types, errors, utilities
- **fable-proto** — Protocol buffers, gRPC, serialization
- **fable-raft** — Consensus (OpenRaft state machine)
- **fable-kvm** — Virtual machine management APIs
- **fable-network** — Network abstractions and protocols
- **fable-storage** — Distributed storage layer

### Services
- **fable-api** — REST/gRPC API server
- **fable-idm** — Identity and access management
- **fable-agent** — Node agent daemon
- **fable-scheduler** — Workload scheduling
- **fable-reconciler** — Control loop (state reconciliation)

## Key Architecture Principles

1. **Libraries provide domain logic** — Each library encapsulates a specific domain
2. **Services consume libraries** — Services only depend on libraries, never other services
3. **Message-based communication** — Services communicate via `fable-proto` messages over gRPC
4. **No inter-service coupling** — Strict dependency rules prevent tight coupling

## Development

```bash
# Build all crates
cargo build

# Run tests
cargo test

# Test specific crate
cargo test -p fable-api

# Format and lint
cargo fmt && cargo clippy
```

See [BUILD.md](docs/how-to/BUILD.md) for complete development workflow.

## Project Structure

```
fable/
├── Cargo.toml              # Workspace root
├── README.md               # This file
├── CLAUDE.md               # Quick reference for Claude Code
├── crates/                 # 11 Rust crates
│   ├── fable-common/       # Shared library
│   ├── fable-proto/        # Protocol definitions
│   ├── fable-raft/         # Consensus
│   ├── fable-kvm/          # VM management
│   ├── fable-network/      # Networking
│   ├── fable-storage/      # Storage
│   ├── fable-api/          # API service
│   ├── fable-idm/          # Identity service
│   ├── fable-agent/        # Agent service
│   ├── fable-scheduler/    # Scheduler service
│   └── fable-reconciler/   # Reconciler service
├── docs/
│   ├── how-to/             # Procedural guides
│   └── reference/          # Reference documentation
└── proto/                  # Protocol buffer definitions (future)
```

## Documentation

- **[docs/README.md](docs/README.md)** — Documentation index
- **[docs/how-to/BUILD.md](docs/how-to/BUILD.md)** — Complete build and development guide
- **[docs/reference/ARCHITECTURE.md](docs/reference/ARCHITECTURE.md)** — Detailed architecture and patterns
- **[CLAUDE.md](CLAUDE.md)** — Quick reference with common commands

## License

Apache License 2.0
