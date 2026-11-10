# Rust for production-grade AI/ML systems engineering

This expands the Rust learning from backend development into production-grade AI/ML systems engineering, organized into four learning tracks:

1. Rust language and engineering fundamentals.
2. Async backend and database engineering.
3. ML inference, model serving and Python interoperability.
4. Production tooling, testing, security and deployment.

The long-term objective: build a Rust-based inference service that can serve ML models, integrate with Python, expose APIs and operate reliably in production.

## 1. Expanded Rust ecosystem roadmap

### Track A – Language, compiler and development tools

| Tool                    | Purpose                                                 |
| ----------------------- | ------------------------------------------------------- |
| `rustc`                 | Rust compiler                                           |
| `cargo`                 | Package manager, build system and dependency management |
| `rustfmt`               | Automatic code formatting                               |
| `clippy`                | Static linting and code-quality suggestions             |
| `cargo test`            | Unit and integration testing                            |
| `cargo audit`           | Detect known security vulnerabilities in dependencies   |
| `cargo build`           | Compile application                                     |
| `cargo build --release` | Optimized production build                              |

Development quality gate:

1. Write code
2. `cargo fmt --check` – formatting
3. `cargo clippy -- -D warnings` – linting
4. `cargo test` – tests
5. `cargo audit` – dependency security
6. Code review – correctness, performance and security
7. `cargo build --release` – production binary

### Track B – Async backend and observability

| Component          | What to learn                                          |
| ------------------ | ------------------------------------------------------ |
| Tokio              | Async runtime, task scheduling, concurrency            |
| Axum               | REST APIs and HTTP handlers                            |
| Middleware         | Authentication, logging, tracing                       |
| SQLx               | Database queries and transactions                      |
| Connection pooling | Database resource management                           |
| `tracing`          | Structured logs, spans and distributed request tracing |

One important concept: **request tracing**. A request travels through:

`API Gateway → Axum → Service → SQLx → PostgreSQL`

We should be able to trace the entire request using a correlation/request ID, record latency, and identify where failures occur.

### Track C – AI inference and model serving

```
Client sends prompt
   ▼
Axum API server              Request handling, authentication, validation
   ▼
Hugging Face Tokenizers      Text → token IDs → attention masks
   ▼
Inference Engine             Candle / Burn · Model weights · Tensor operations
   ▼
Generation / Decoding        Logits → sampling → token IDs → text
   ▼
JSON response
```

| Technology              | Role                                                      |
| ----------------------- | --------------------------------------------------------- |
| Hugging Face Tokenizers | Fast tokenization and preprocessing                       |
| Candle                  | Native Rust ML inference and tensor operations            |
| Burn                    | Deep learning framework supporting training and inference |

Candle is particularly relevant for deploying transformer models directly in Rust. Burn is worth exploring for training-oriented workflows too.

Inference engineering topics to cover:

1. Tokenization and detokenization.
2. Model loading and weight formats (Safetensors).
3. Tensor operations.
4. CPU versus GPU inference.
5. Batch inference.
6. Streaming token generation.
7. KV cache.
8. Quantization.
9. Throughput, latency and memory profiling.
10. Model serving and concurrency management.

### Track D – Rust + Python interoperability

```
Python application (FastAPI · PyTorch · NumPy)
   ▼
PyO3          Python ↔ Rust bindings
   ▼
Rust library  High-performance computation

maturin builds and packages Rust code as installable Python packages (wheels).
```

Learn these tools in this order:

1. PyO3 – expose Rust functions and classes to Python.
2. maturin – compile and package the Rust extension.
3. Python ↔ Rust data conversion.
4. Error handling across language boundaries.
5. Performance benchmarking against pure Python.
6. GIL considerations and concurrent execution.

For example, implement a high-performance feature extraction or token-processing module in Rust and import it into an existing Python ML application.

## 2. Production engineering practices

These should be part of every project, not an afterthought.

| Area           | Engineering practice                                 |
| -------------- | ---------------------------------------------------- |
| Memory safety  | Prefer safe Rust; understand ownership and borrowing |
| Error handling | `Result`, `Option`, `thiserror`                      |
| Testing        | Unit, integration and regression tests               |
| Security       | `cargo audit`, dependency review                     |
| Observability  | `tracing`, structured logs, spans                    |
| Performance    | Benchmarking, profiling, memory monitoring           |
| Deployment     | Release binary, Docker, CI/CD                        |
| Code review    | Review, test, fallback and rollback changes          |

**Safe Rust:** Understand why Rust prevents data races and many memory-safety errors at compile time. Learn what `unsafe` does, but don't use it casually.

**Fallback changes:** Every optimization should have a safe fallback. For example, if GPU inference is unavailable, the service could route to CPU inference, provided the model and deployment support it.

## 3. Recommended hands-on projects

| # | Project                          | Stack                                              | Goal |
| - | -------------------------------- | -------------------------------------------------- | ---- |
| 1 | Rust REST API                    | Axum + Serde + Tokio + SQLx + PostgreSQL           | Inventory API with middleware and connection pooling |
| 2 | Rust LLM Inference Server        | Hugging Face Tokenizers + Candle                   | Load a model, tokenize prompts, generate tokens, expose streaming inference APIs |
| 3 | Python/Rust Hybrid ML Engine     | PyO3 + maturin                                     | Compute-intensive functions in Rust exposed to Python |
| 4 | Training with Burn               | Burn                                               | Small neural network, training loop, evaluation, model serialization |

## 4. Reference documentation

- [The Rust Book](https://doc.rust-lang.org/book/)
- [Tokio](https://docs.rs/tokio/latest/tokio/)
- [Axum](https://docs.rs/axum/latest/axum/)
- [Serde](https://serde.rs/)
- [SQLx](https://github.com/launchbadge/sqlx)
- [PyO3](https://pyo3.rs/)
- [maturin](https://www.maturin.rs/)
- [Candle](https://huggingface.co/docs/candle)
- [Burn](https://burn.dev/)

## 5. Suggested learning sequence

Avoid learning all these frameworks simultaneously.

| Phase | Focus                                        | Priority  |
| ----- | -------------------------------------------- | --------- |
| 1     | Rust fundamentals, ownership, traits         | Essential |
| 2     | Cargo, Clippy, fmt, tests, audit             | Essential |
| 3     | Serde, typed data, error handling            | Essential |
| 4     | Tokio, async, concurrency                    | Essential |
| 5     | Axum, middleware, tracing                    | Essential |
| 6     | SQLx, connection pooling                     | Essential |
| 7     | PyO3, maturin                                | High      |
| 8     | Tokenizers, Candle                           | High      |
| 9     | Burn, training                               | Advanced  |
| 10    | Production serving, benchmarking, deployment | Advanced  |

> **Open question:** "pangora" in the original notes — possibly [Pingora](https://github.com/cloudflare/pingora) (Cloudflare's Rust framework for building proxies)? To confirm before adding to the roadmap.

**First coding lesson:** Rust ownership, borrowing and lifetimes, followed by Serde and typed API data.
