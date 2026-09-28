# Building Fable

This guide covers building, testing, and maintaining code quality in the Fable workspace.

## Quick Start

```bash
# Debug build all crates
cargo build

# Optimized release build
cargo build --release

# Run all tests
cargo test

# Run tests for a specific crate
cargo test -p fable-api

# Run a specific test by name
cargo test test_name

# Format code
cargo fmt

# Lint with clippy
cargo clippy

# Fast type checking without building
cargo check
```

## Building Specific Crates

Navigate to a crate directory and use the `-p` flag:

```bash
cd crates/fable-api
cargo build -p fable-api
cargo test -p fable-api
```

## Running Services

Each service can be built and run as a standalone binary:

```bash
# Build and run a service
cargo run --release -p fable-api
cargo run --release -p fable-scheduler
```

## Code Quality

### Formatting
Code is formatted with rustfmt according to Rust style conventions:

```bash
cargo fmt              # Format all code
cargo fmt --check      # Check formatting without modifying
```

### Linting
Use clippy to catch common mistakes and improve code:

```bash
cargo clippy           # Check all crates
cargo clippy -p fable-api  # Check specific crate
```

## Development Workflow

1. **Make changes** in your crate
2. **Type check frequently**: `cargo check -p <crate>`
3. **Run tests locally**: `cargo test -p <crate>`
4. **Format before commit**: `cargo fmt`
5. **Final lint pass**: `cargo clippy`

## Build Profiles

The workspace defines optimized release settings in `Cargo.toml`:

- **Debug build** (`cargo build`): Fast compilation, easier debugging
- **Release build** (`cargo build --release`): Optimized with LTO, used for production

Release builds enable:
- `opt-level = 3` — Maximum optimization
- `lto = true` — Link-time optimization for better runtime performance
- `codegen-units = 1` — Slower compilation, better optimization
