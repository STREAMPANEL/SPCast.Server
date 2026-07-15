# Project Dependencies and Technologies

## Programming Languages
- **Rust**: Primary language for the backend (server logic, streaming, endpoints).
- **HTML / CSS / JavaScript**: Used for the frontend (web dashboard and error pages).

### Web Framework & Server

| Library | Version | License | Purpose |
|---|---|---|---|
| **axum** | `0.8.8` | MIT | Ergonomic and modular web framework based on Tokio, Tower, and Hyper |
| **axum-server** | `0.8.0` | MIT | Server implementation for Axum with TLS support (rustls) |
| **tower** | `0.5.3` | MIT | Robust middleware components (timeouts, limits, load-shedding) |
| **tower-http** | `0.6.8` | MIT | HTTP-specific middleware (CORS, static file serving) |
| **rust-embed** | `8.11.0` | MIT | Embedding of static web assets and translation files into the binary |
| **urlencoding** | `2.1.3` | MIT | Encoding and decoding for URLs |

### Asynchronous Runtime Environment

| Library | Version | License | Purpose |
|---|---|---|---|
| **tokio** | `1.50.0` | MIT | Event-driven, asynchronous platform for Rust |
| **tokio-stream** | `0.1.18` | MIT | Stream utilities for Tokio |
| **futures** | `0.3.32` | MIT / Apache-2.0 | Abstractions for asynchronous programming |

### Data Processing & Serialization

| Library | Version | License | Purpose |
|---|---|---|---|
| **serde** | `1.0.228` | MIT / Apache-2.0 | Framework for serializing and deserializing data structures |
| **serde_json** | `1.0.149` | MIT / Apache-2.0 | JSON serialization and deserialization |
| **quick-xml** | `0.39.2` | MIT | High-performance XML parser and serializer |
| **base64** | `0.22.1` | MIT / Apache-2.0 | Encoding and decoding for Base64 |
| **hex** | `0.4.3` | MIT / Apache-2.0 | Hexadecimal encoding and decoding |
| **bytes** | `1.11.1` | MIT | Efficient handling of byte buffers |
| **uuid** | `1.21.0` | MIT / Apache-2.0 | Generation and parsing of UUIDs (v4/v5) |
| **maxminddb** | `0.29.0` | MIT | Local GeoIP2/GeoLite2 country and city database reader |
| **regex** | `1.12.3` | MIT / Apache-2.0 | Regular expression support |
| **polib** | `0.3.0` | MIT | Parsing and manipulation of `.po` translation files (i18n) |

### Network & Protocols

| Library | Version | License | Purpose |
|---|---|---|---|
| **reqwest** | `0.13.2` | MIT / Apache-2.0 | Asynchronous HTTP client (webhooks, analytics, license verification) |
| **srt-tokio** | `0.4.4` | MIT | Secure Reliable Transport (SRT) protocol implementation |
| **http-body-util** | `0.1.3` | MIT | Helper functions for processing HTTP bodies |

### Concurrency & State Management

| Library | Version | License | Purpose |
|---|---|---|---|
| **dashmap** | `6.1.0` | MIT | Fast, thread-safe concurrent hash map |
| **parking_lot** | `0.12.5` | MIT / Apache-2.0 | High-performance mutexes and RwLocks |

### Security & Licensing

| Library | Version | License | Purpose |
|---|---|---|---|
| **ed25519-dalek** | `2` | MIT / Apache-2.0 | Ed25519 cryptographic signatures for license verification |
| **obfstr** | `0.4.4` | MIT / Apache-2.0 | Compile-time string obfuscation for anti-piracy |
| **md-5** | `0.10.6` | MIT / Apache-2.0 | MD5 hashing for legacy/specific operations |
| **hmac** | `0.12.1` | MIT / Apache-2.0 | Keyed pseudonymous analytics visitor identifiers |
| **sha2** | `0.10.9` | MIT / Apache-2.0 | SHA-256 used by analytics HMAC identifiers |

### System & Utilities

| Library | Version | License | Purpose |
|---|---|---|---|
| **anyhow** | `1.0.102` | MIT / Apache-2.0 | Flexible error handling |
| **chrono** | `0.4.44` | MIT / Apache-2.0 | Date and time calculations |
| **fastrand** | `2.3.0` | MIT / Apache-2.0 | Fast non-cryptographic random number generator |
| **rand** | `0.10.0` | MIT / Apache-2.0 | Cryptographic-grade random number generation |

### Logging & Diagnostics

| Library | Version | License | Purpose |
|---|---|---|---|
| **tracing** | `0.1.44` | MIT | Structured logging and tracing for async applications |
| **tracing-appender** | `0.2.4` | MIT | Non-blocking log appender for file output |
| **tracing-subscriber** | `0.3.22` | MIT | Filtering and formatting of trace data |
| **rolling-file** | `0.2.0` | MIT | Log rotation for file-based logs |

## Frontend Libraries

| Library | Version | License | Purpose |
|---|---|---|---|
| **hls.js** | `1.x` (minified) | Apache-2.0 | JavaScript library for HLS playback in the browser interface. |
