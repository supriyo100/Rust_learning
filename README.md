# Rust_learning

Learning Rust for production-grade backend and AI/ML systems engineering, using Python (FastAPI, Pydantic, asyncio) equivalents as reference points.

## Notes

1. [Rust backend roadmap](notes/01-rust-backend-roadmap.md): Serde, Tokio, Axum, SQLx, and Lesson 1 (typed data vs JSON)
2. [Rust AI/ML ecosystem roadmap](notes/02-rust-ai-ml-ecosystem-roadmap.md): tooling, observability, Candle/Burn inference, PyO3/maturin

## Toolchain

- Rust stable (via rustup): `rustc`, `cargo`, `rustfmt`, `clippy`, `rust-analyzer`
- Quality gate: `cargo fmt --check` → `cargo clippy -- -D warnings` → `cargo test` → `cargo audit` → `cargo build --release`

## Stack

| Rust  | Python equivalent    |
| ----- | -------------------- |
| Serde | Pydantic             |
| Axum  | FastAPI              |
| Tokio | asyncio              |
| SQLx  | SQLAlchemy / asyncpg |
