# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Documentation Map

- **[BUILD.md](docs/how-to/BUILD.md)** — Building, testing, and development workflow
- **[ARCHITECTURE.md](docs/reference/ARCHITECTURE.md)** — Crate organization, design patterns, and dependency guidelines

## Quick Reference

**This is a Rust workspace** organized as libraries + services:

- **Libraries**: `fable-common`, `fable-proto`, `fable-raft`, `fable-kvm`, `fable-network`, `fable-storage`
- **Services**: `fable-api`, `fable-idm`, `fable-agent`, `fable-scheduler`, `fable-reconciler`

**Key rule**: Services only depend on libraries. No service-to-service imports. Use `fable-proto` messages for inter-service communication.

**Workspace structure**:
```
crates/              # 11 crates (6 libraries + 5 services)
docs/
├── how-to/          # Procedural guides (BUILD.md)
└── reference/       # Reference docs (ARCHITECTURE.md)
```

See [BUILD.md](docs/how-to/BUILD.md) for complete build and test commands.
