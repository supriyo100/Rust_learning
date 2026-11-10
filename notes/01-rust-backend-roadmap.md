# Learning Rust for production-grade backend engineering

Let's learn Rust from an engineering perspective, especially how to build high-performance, asynchronous, type-safe backend services.

Since you already work with Python, FastAPI, React, ML systems and distributed architectures, we'll use Python equivalents to understand Rust concepts faster.

The objective is not just to learn Rust syntax. It is to understand how to build a production-grade Rust API service.

## 1. Our learning roadmap

1. **Rust fundamentals** — Variables, mutability, ownership, borrowing, references, lifetimes, structs, enums, traits, generics and error handling.
2. **Typed data and Serde** — Serialization, deserialization, JSON, structs, enums, validation and type-safe request/response models.
3. **Tokio – Async runtime** — Futures, async/await, tasks, spawning, concurrency, synchronization and runtime management.
4. **Axum – Web framework** — Routing, handlers, extractors, middleware, state management, request lifecycle and error handling.
5. **SQLx – Database layer** — PostgreSQL, compile-time checked queries, transactions, connection pooling and migrations.
6. **Production architecture** — Layered architecture, repository pattern, dependency injection, observability, graceful shutdown and testing.

## 2. Understand the technology stack

### Rust backend architecture

```
Client / Frontend            React, Python client, ML service
        │
        ▼
Axum – HTTP API              Routes · Handlers · Extractors · Middleware
        │
        ▼
Serde – Typed data           JSON ⇄ Rust structs and enums
        │
        ▼
Application / Business Logic Services · Domain models · Validation
        │
        ▼
SQLx + Connection Pool       Async queries · Transactions · PostgreSQL
        │
        ▼
PostgreSQL

Tokio (async runtime) runs async tasks and coordinates non-blocking I/O
across the whole application.
```

| Technology      | Responsibility                  | Python equivalent    |
| --------------- | ------------------------------- | -------------------- |
| Serde           | Serialization / deserialization | Pydantic             |
| Axum            | HTTP framework                  | FastAPI              |
| Tokio           | Async runtime                   | asyncio              |
| SQLx            | Async SQL access                | SQLAlchemy / asyncpg |
| Connection Pool | Reuse DB connections            | SQLAlchemy pool      |
| Middleware      | Cross-cutting HTTP logic        | FastAPI middleware   |

One important distinction: Serde is primarily a serialization framework, not a complete validation framework like Pydantic. We'll explore this difference.

## 3. Lesson 1: Typed data vs JSON

Let's start here because this is foundational to understanding Serde.

### A. What happens in a Python API?

Imagine a client sends:

```json
{
  "sku": "INSULIN-001",
  "quantity": 100,
  "warehouse": "HYD-01"
}
```

In Python/FastAPI:

```python
from pydantic import BaseModel

class InventoryRequest(BaseModel):
    sku: str
    quantity: int
    warehouse: str
```

Pydantic converts incoming JSON into a typed Python object and validates its fields.

### B. What happens in Rust?

Rust uses structs to represent strongly typed data.

```rust
struct InventoryRequest {
    sku: String,
    quantity: u32,
    warehouse: String,
}
```

But how does Rust convert incoming JSON into this struct? That's where Serde comes in.

### C. Understanding Serde

Serde stands for **ser**ialization and **de**serialization.

- Serialization: Rust struct → JSON
- Deserialization: JSON → Rust struct

```
JSON input (unstructured external representation)
   │  Deserialize (Serde)
   ▼
InventoryRequest (typed Rust struct)
   │
   ▼
Business logic
```

### D. Implement Serde

Add these dependencies in `Cargo.toml`:

```toml
[dependencies]
serde = { version = "1", features = ["derive"] }
serde_json = "1"
```

Rust code:

```rust
use serde::{Deserialize, Serialize};

#[derive(Debug, Serialize, Deserialize)]
struct InventoryRequest {
    sku: String,
    quantity: u32,
    warehouse: String,
}

fn main() {
    let json = r#"{
        "sku": "INSULIN-001",
        "quantity": 100,
        "warehouse": "HYD-01"
    }"#;

    let request: InventoryRequest =
        serde_json::from_str(json).unwrap();

    println!("{:?}", request);

    let output = serde_json::to_string(&request).unwrap();

    println!("{}", output);
}
```

Understand these three things:

1. `struct`: Defines the data structure.
2. `Deserialize`: Allows JSON to become a Rust object.
3. `Serialize`: Allows a Rust object to become JSON.

`derive` automatically generates the implementation of these traits.

> **Important:** If `quantity` contains a string instead of a valid integer, deserialization fails. Missing required fields also cause errors unless defaults or optional fields are configured.

## 4. Important: Struct vs Enum vs JSON

In production systems, you should understand these three data representations.

**Struct – fixed data structure**

```rust
struct Product {
    sku: String,
    quantity: u32,
}
```

**Enum – controlled set of possible states**

```rust
enum InventoryStatus {
    Available,
    Reserved,
    OutOfStock,
}
```

Unlike a free-form string, an enum restricts the possible values.

**Optional data – `Option`**

```rust
struct Product {
    sku: String,
    quantity: u32,
    description: Option<String>,
}
```

`Option<String>` means the description may or may not exist. This is particularly useful when designing APIs and database models.

## 5. What we'll learn in each framework

| Concept         | What you need to understand                                            |
| --------------- | ---------------------------------------------------------------------- |
| Serde           | `Serialize`, `Deserialize`, `derive`, attributes, custom serialization |
| Axum            | Router, State, Handler, Extractor, Json                                |
| Axum middleware | Authentication, authorization, logging, tracing, rate limiting, CORS   |
| Tokio           | Runtime, Future, async/await, spawn, JoinHandle, channels              |
| SQLx            | Query, Execute, Transaction, FromRow                                   |
| Connection pool | Acquire, release, max connections, timeout, pool lifecycle             |

### How middleware works in Axum

```
Incoming HTTP request
   ▼
Authentication middleware      Validate JWT / API key
   ▼
Logging / Tracing middleware   Request ID, latency, logs
   ▼
Axum Handler                   Execute business logic
   ▼
Response
```

We'll implement this practically using Axum's middleware and Tower ecosystem.

## 6. Your first Rust environment

Install Rust using [rustup](https://rustup.rs/).

Verify:

```
rustc --version
cargo --version
```

Create your first project:

```
cargo new rust_backend
cd rust_backend
cargo run
```

Cargo is Rust's package manager and build system.

Useful commands:

```
cargo check
cargo build
cargo run
cargo test
cargo add serde --features derive
```

## 7. Our practical project

We'll build an **Inventory Control Tower API**, which will make the concepts relevant to real production systems.

| Module                  | Implementation                   |
| ----------------------- | -------------------------------- |
| Product API             | Axum                             |
| Request/response models | Serde                            |
| Async execution         | Tokio                            |
| Database                | PostgreSQL                       |
| Database access         | SQLx                             |
| Connection management   | SQLx Pool                        |
| Middleware              | Authentication, logging, tracing |
| Error handling          | Result, anyhow, thiserror        |
| Testing                 | Unit + integration tests         |

## 8. First exercise

Before moving to Axum, try these three tasks.

- [ ] Create a `Product` struct with `sku`, `name` and `quantity`. Use `String` and `u32`.
- [ ] Add an optional `description` field. Use `Option<String>`.
- [ ] Serialize and deserialize `Product` using Serde. Use `serde_json::to_string` and `from_str`.

**Recommended learning order:** Don't jump directly into Axum and Tokio. First understand Rust ownership, borrowing, references, traits and `Result<T, E>`. These concepts explain why Rust backend development differs from Python.

**Next lesson:** Rust Ownership → Borrowing → References → Lifetimes, with Python comparisons and practical code.
