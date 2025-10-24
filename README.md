# Puffin 🐧
...because kv paired databases don't exist yet  
[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-ffdd00?&logo=buy-me-a-coffee&logoColor=black)](buymeacoffee.com/timothyelems)

[![Rust Version](https://img.shields.io/badge/rust-1.82%2B-orange.svg)](https://www.rust-lang.org)
[![License: AGPL v3](https://img.shields.io/badge/License-AGPL%20v3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)
[![Commercial License](https://img.shields.io/badge/Commercial-License%20Available-green.svg)](mailto:commercial@puffin.dev)
[![Build Status](https://img.shields.io/github/actions/workflow/status/TimothyElems/Puffin/ci.yml?branch=main&label=build)](https://github.com/TimothyElems/Puffin/actions)
[![Crates.io](https://img.shields.io/crates/v/puffin.svg)](https://crates.io/crates/puffin)
[![docs.rs](https://img.shields.io/docsrs/puffin/latest)](https://docs.rs/puffin)
![GitHub commit activity](https://img.shields.io/github/commit-activity/m/TimothyElems/Puffin)
<!-- ![GitHub Sponsors](https://img.shields.io/github/sponsors/TimothyElems) -->


# Puffin

Puffin is a simple, distributed key-value store written in Rust, designed to explore Rust's concurrency, networking, and safety features. Inspired by the loyal pair-bonding of Atlantic puffins, this project implements a TCP-based server that stores key-value pairs, handles concurrent client requests, persists data to disk, and supports basic leader-follower replication.

Built over a weekend as a learning tool and portfolio piece for low-level, networking, and distributed systems development, Puffin has evolved into a production-capable system with enterprise features available under a commercial license.

## ✨ Features

### Open Source Features (AGPL v3)
- **Key-Value Storage**: Supports `SET`, `GET`, and `DELETE` operations for string key-value pairs
- **Concurrent Request Handling**: Uses Tokio async runtime and Arc<RwLock> to safely process multiple client requests
- **TCP Networking**: Implements a text-based protocol over TCP for client-server communication
- **Basic Replication**: Forwards writes (`SET`, `DELETE`) from leader to follower nodes for data redundancy
- **CLI Client**: Command-line interface for easy interaction with the server
- **Persistence**: JSON-based data persistence with durability across server restarts
- **Graceful Shutdown**: Clean server shutdown with signal handling
- **Basic Metrics**: Prometheus metrics for monitoring performance

### Enterprise Features (Commercial License)
- **Advanced Clustering**: Raft consensus for leader election and fault tolerance
- **Role-Based Access Control (RBAC)**: Fine-grained permissions and user management
- **Advanced Monitoring**: Rich dashboards and alerting capabilities  
- **Automated Backup & Restore**: Point-in-time recovery and scheduled backups
- **Enterprise Security**: TLS encryption, audit logging, and advanced authentication
- **Multi-Region Replication**: Cross-datacenter synchronization
- **Priority Support**: Professional support, consulting, and training

## 🎯 Project Goals

Puffin was built to achieve fluency in Rust over a weekend, focusing on:
- Rust syntax, ownership, borrowing, and lifetimes
- Async/await with Tokio runtime
- TCP networking using `tokio::net`
- Fearless concurrency with Arc, Mutex, and RwLock
- Basic distributed systems concepts like leader-follower replication
- Error handling with Result and custom error types
- Serialization with serde

This project aligns with low-level, networking, and distributed systems roles, showcasing practical skills in Rust for building networked applications.

## 📋 Prerequisites

- **Rust**: Version 1.75 or later ([install from rustup.rs](https://rustup.rs))
- **Tools**: Terminal and optional tools like `telnet` or `nc` (netcat) for manual testing
- **OS**: Tested on Linux/macOS; should work on Windows with minor adjustments

## 🚀 Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/puffin.git
cd puffin

# Build binaries (release mode)
cargo build --release

# Or use the Makefile
make build
```

### Basic Usage

1. **Start the server:**
   ```bash
   ./target/release/puffin-server
   # Server starts on :8080 by default
   ```

2. **Use the CLI client:**
   ```bash
   # Set a key-value pair
   ./target/release/puffin-cli set mykey "Hello, Puffin!"
   
   # Get a value
   ./target/release/puffin-cli get mykey
   # Output: Hello, Puffin!
   
   # Delete a key
   ./target/release/puffin-cli delete mykey
   # Output: OK
   ```

3. **Manual testing with telnet:**
   ```bash
   telnet localhost 8080
   SET name puffin
   GET name
   DELETE name
   ```

## 📖 Usage

### Running the Leader Server

Start the leader server with default settings:
```bash
./target/release/puffin-server
```

Customize configuration:
```bash
./target/release/puffin-server --port 8081 --data mystore.json --follower localhost:8082
```

### Running a Follower Server

Start a follower server on a different port:
```bash
./target/release/puffin-server --port 8081 --data follower.json --mode follower
```

The leader automatically forwards `SET` and `DELETE` commands to configured followers.

### CLI Client Options

```bash
# Basic operations
./target/release/puffin-cli --addr localhost:8080 set foo bar
./target/release/puffin-cli --addr localhost:8080 get foo
./target/release/puffin-cli --addr localhost:8080 delete foo

# Batch operations
./target/release/puffin-cli --addr localhost:8080 --file commands.txt

# JSON output for scripting
./target/release/puffin-cli --addr localhost:8080 get foo --format json
```

### Configuration File

Create a `config.toml` file for advanced configuration:
```toml
[server]
port = 8080
data_file = "puffin.json"

[replication]
mode = "leader"
followers = ["localhost:8081", "localhost:8082"]

[logging]
level = "info"
format = "json"

[metrics]
enabled = true
port = 9090
```

Run with config:
```bash
./target/release/puffin-server --config config.toml
```

## 📊 Example Session

```bash
# Terminal 1: Start leader
$ ./target/release/puffin-server
2025-01-15 10:30:00 INFO Server starting on :8080

# Terminal 2: Start follower  
$ ./target/release/puffin-server --port 8081 --mode follower
2025-01-15 10:30:05 INFO Follower server starting on :8081

# Terminal 3: Use CLI client
$ ./target/release/puffin-cli set greeting "Hello, distributed world!"
OK

$ ./target/release/puffin-cli get greeting
Hello, distributed world!

$ ./target/release/puffin-cli delete greeting
OK

# Verify replication worked
$ ./target/release/puffin-cli --addr localhost:8081 get greeting
ERROR: Key not found (confirming deletion was replicated)
```

## 📁 Project Structure

```
puffin/
├── src/                    # Source code
│   ├── bin/               # Binary entry points
│   │   ├── server.rs      # Server binary
│   │   └── cli.rs         # CLI client binary
│   ├── store/             # Core key-value store logic
│   ├── server/            # TCP server implementation  
│   ├── client/            # Client implementation
│   ├── config/            # Configuration management
│   ├── metrics/           # Monitoring and metrics
│   ├── error.rs           # Error types
│   └── lib.rs             # Public library interface
├── enterprise/            # Enterprise features (commercial)
│   ├── monitoring/        # Advanced monitoring
│   ├── security/          # RBAC and encryption
│   ├── clustering/        # Raft consensus
│   └── backup/            # Backup and restore
├── docs/                  # Documentation
├── examples/              # Usage examples
├── deployments/           # Kubernetes, Docker configs
├── tests/                 # Integration tests
├── benches/               # Benchmarks
├── Cargo.toml             # Dependencies and metadata
└── Cargo.lock             # Locked dependencies
```

## 🔧 Development

### Setup Development Environment

```bash
make setup-dev
# Or manually:
rustup component add rustfmt clippy
cargo install cargo-watch cargo-tarpaulin
```

### Run Tests

```bash
# Unit tests
cargo test

# Integration tests  
cargo test --test '*'

# With test coverage
cargo tarpaulin --out Html

# Or use the Makefile
make test
make coverage
```

### Code Quality

```bash
# Linting with Clippy
cargo clippy -- -D warnings

# Formatting
cargo fmt

# Check formatting
cargo fmt -- --check

# Security audit
cargo audit

# Or use the Makefile
make lint
make fmt
make security
```

### Build and Release

```bash
# Debug build
cargo build

# Release build
cargo build --release

# Build for multiple platforms
cargo build --release --target x86_64-unknown-linux-gnu
cargo build --release --target x86_64-apple-darwin
cargo build --release --target x86_64-pc-windows-gnu

# Or use the Makefile
make build
make build-all
make release VERSION=v1.0.0
```

### Development Workflow

```bash
# Watch mode - auto-rebuild on changes
cargo watch -x check -x test -x run

# Run with logs
RUST_LOG=debug cargo run --bin puffin-server
```

## 🏗️ Architecture

Puffin follows a simple but effective architecture:

- **Tokio Runtime**: Async/await for concurrent TCP connections
- **Key-Value Store**: Thread-safe in-memory storage with Arc<RwLock<HashMap>>
- **Replication**: Asynchronous leader-follower replication over TCP
- **Persistence**: JSON-based storage with atomic writes using serde
- **Protocol**: Text-based command protocol for simplicity
- **Error Handling**: Custom error types with thiserror for ergonomic error management

For detailed architecture documentation, see [docs/architecture.md](docs/architecture.md).

## 📈 Performance

Basic performance characteristics (on a modern laptop):

- **Throughput**: ~100,000 operations/second (single node, due to Rust's zero-cost abstractions)
- **Latency**: <500μs average for local operations
- **Memory**: ~80MB for 1M key-value pairs (efficient memory usage)
- **Persistence**: ~2000 writes/second to disk

Run benchmarks:
```bash
cargo bench
# Or
make bench
```

## 🛡️ Security

### Open Source Security
- Memory safety guaranteed by Rust's ownership system
- No null pointer dereferences or buffer overflows
- Thread safety enforced at compile time
- Basic input validation and sanitization
- Graceful error handling with Result types

### Enterprise Security (Commercial License)
- TLS encryption for all network communication (via rustls)
- Role-based access control (RBAC)
- Audit logging and compliance reporting
- Advanced authentication mechanisms

Report security issues: [security@puffin.dev](mailto:security@puffin.dev)

## ⚠️ Current Limitations

**Important**: The open source version is designed as a learning project and has intentional limitations:

- **Scope**: Weekend learning project, not production-ready without enterprise features
- **Replication**: Basic leader-follower model; no automatic failover or leader election
- **Persistence**: Simple JSON-based storage; not optimized for large datasets  
- **Security**: No authentication or encryption in open source version
- **Consistency**: Eventually consistent replication; no strong consistency guarantees
- **Scalability**: Single-node storage; no sharding or horizontal scaling

**For production use**, consider the [enterprise version](#enterprise-features-commercial-license) which addresses these limitations.

## 🚀 Production Readiness

To make Puffin production-ready, the enterprise version includes:

- **Fault Tolerance**: Raft consensus for leader election and automatic failover
- **Performance**: Optimized storage backend with memory-mapped files and connection pooling
- **Security**: TLS encryption with rustls, authentication, and access control
- **Monitoring**: Rich metrics, dashboards, and alerting
- **Scalability**: Clustering, sharding, and load balancing
- **Operations**: Backup/restore, rolling updates, and configuration management

## 📄 License

Puffin is dual-licensed to support both open source and commercial use cases:

### Open Source License (AGPL v3)
The core Puffin software is licensed under the **GNU Affero General Public License v3** (AGPL v3). This means:

- ✅ **Free to use** for personal, educational, and internal business purposes
- ✅ **Free to modify** and distribute under AGPL v3 terms  
- ⚠️ **Network copyleft**: If you offer Puffin as a service over a network, you must provide the complete source code to users
- ⚠️ **Derivative works** must also be licensed under AGPL v3

### Commercial License
For organizations that cannot comply with AGPL v3 requirements, we offer a **Commercial License** that provides:

- ✅ **No copyleft obligations** - use Puffin without releasing your source code
- ✅ **Enterprise features** - advanced monitoring, RBAC, clustering, backup/restore
- ✅ **Commercial support** - priority support, consulting, and training  
- ✅ **Hosted service rights** - offer Puffin as part of your SaaS platform

### Choosing the Right License

| Use Case | Recommended License |
|----------|-------------------|
| Learning, research, internal tools | AGPL v3 (Free) |
| Open source project/contribution | AGPL v3 (Free) |
| Commercial SaaS offering | Commercial License |
| Enterprise deployment | Commercial License |
| Closed-source product integration | Commercial License |

### Getting a Commercial License

For commercial licensing inquiries:
- 📧 **Email**: [commercial@puffin.dev](mailto:commercial@puffin.dev)  
- 🌐 **Website**: [https://puffin.dev/commercial](https://puffin.dev/commercial)
- 📄 **Terms**: [LICENSE-COMMERCIAL](LICENSE-COMMERCIAL)

## 🤝 Contributing

We welcome contributions from the community! Puffin follows an open development model where community contributions help improve both the open source and commercial versions.

### Quick Contribution Guide

1. **Sign the CLA**: Contributors must sign our [Contributor License Agreement](CONTRIBUTING.md#contributor-license-agreement-cla)
2. **Fork & Clone**: Fork the repo and create a feature branch
3. **Develop**: Make your changes following our [contribution guidelines](CONTRIBUTING.md)
4. **Test**: Ensure tests pass with `cargo test` and add new tests for your changes
5. **Format**: Run `cargo fmt` and `cargo clippy` before submitting
6. **Submit**: Create a pull request with a clear description

### Types of Contributions
- 🐛 **Bug fixes** and reliability improvements
- ⚡ **Performance optimizations** 
- 📚 **Documentation** and examples
- 🧪 **Tests** and benchmarks
- ✨ **New features** for the core system
- 🏢 **Enterprise features** (special recognition for commercial contributors)

See our [Contributing Guide](CONTRIBUTING.md) for detailed information.

### Contributors

Thank you to all our contributors! See [CONTRIBUTORS.md](CONTRIBUTORS.md) for the full list.

## 🆘 Support

### Community Support (Free)
- 📖 **Documentation**: [docs/](docs/) directory and [puffin.dev/docs](https://puffin.dev/docs)
- 💬 **GitHub Discussions**: Ask questions and share ideas
- 🐛 **GitHub Issues**: Report bugs and request features
- 📧 **Community Email**: [community@puffin.dev](mailto:community@puffin.dev)

### Commercial Support
- 🏢 **Enterprise Support**: Priority support with SLA guarantees
- 📞 **Professional Services**: Consulting, training, and custom development
- 🚀 **Migration Assistance**: Help moving from other key-value stores
- 📊 **Performance Optimization**: Tuning and scaling guidance

Contact [commercial@puffin.dev](mailto:commercial@puffin.dev) for enterprise support options.

## 🛣️ Roadmap

### Open Source (AGPL v3)
- [ ] HTTP API alongside TCP protocol (using axum)
- [ ] WebAssembly client library  
- [ ] Improved CLI with shell completion (using clap)
- [ ] Basic clustering documentation
- [ ] Performance optimizations with zero-copy operations

### Enterprise (Commercial License)
- [ ] Multi-region replication
- [ ] Advanced query capabilities  
- [ ] Kubernetes operator
- [ ] Grafana dashboard templates
- [ ] Advanced backup strategies

See our [public roadmap](https://github.com/yourusername/puffin/projects) for detailed planning.

## 🔗 Links

- **Website**: [https://puffin.dev](https://puffin.dev)
- **Documentation**: [https://docs.puffin.dev](https://docs.puffin.dev) 
- **Commercial**: [https://puffin.dev/commercial](https://puffin.dev/commercial)
- **Crates.io**: [https://crates.io/crates/puffin-kv](https://crates.io/crates/puffin-kv)
- **GitHub**: [https://github.com/yourusername/puffin](https://github.com/yourusername/puffin)
- **Docker Hub**: [https://hub.docker.com/r/puffindev/puffin](https://hub.docker.com/r/puffindev/puffin)

## 🙏 Acknowledgments

- **Inspiration**: Atlantic puffins' loyal pair-bonding, reflecting the paired (key-value) and cooperative (distributed) nature of this project
- **Learning**: Built as a portfolio piece for low-level, networking, and distributed systems roles
- **Rust Community**: For creating an amazing language and ecosystem for systems programming
- **Community**: Thank you to all contributors, users, and supporters of the Puffin project

---

**Made with ❤️ and Rust 🦀**
[Support](https://coindrop.to/timothyelems)

*Puffin: ...because kv paired databases don't exist yet.*
