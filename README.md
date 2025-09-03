# Puffin 🐧
...because kv paired databases don't exist yet  
[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-ffdd00?&logo=buy-me-a-coffee&logoColor=black)](buymeacoffee.com/timothyelems)

[![Go Version](https://img.shields.io/badge/go-1.22+-blue.svg)](https://golang.org)
[![License: AGPL v3](https://img.shields.io/badge/License-AGPL%20v3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)
[![Commercial License](https://img.shields.io/badge/Commercial-License%20Available-green.svg)](mailto:commercial@puffin.dev)
[![Build Status](https://img.shields.io/github/actions/workflow/status/yourusername/puffin/ci.yml?branch=main)](https://github.com/yourusername/puffin/actions)
[![Go Report Card](https://goreportcard.com/badge/github.com/yourusername/puffin)](https://goreportcard.com/report/github.com/yourusername/puffin)
![GitHub commit activity](https://img.shields.io/github/commit-activity/:interval/TimothyElems/Puffin)
<!-- ![GitHub Sponsors](https://img.shields.io/github/sponsors/TimothyElems) -->



Puffin is a simple, distributed key-value store written in Go, designed to explore Go's concurrency, networking, and standard library features. Inspired by the loyal pair-bonding of Atlantic puffins, this project implements a TCP-based server that stores key-value pairs, handles concurrent client requests, persists data to disk, and supports basic leader-follower replication.

Built over a weekend as a learning tool and portfolio piece for low-level, networking, and distributed systems development, Puffin has evolved into a production-capable system with enterprise features available under a commercial license.

## ✨ Features

### Open Source Features (AGPL v3)
- **Key-Value Storage**: Supports `SET`, `GET`, and `DELETE` operations for string key-value pairs
- **Concurrent Request Handling**: Uses Go goroutines and mutexes to safely process multiple client requests
- **TCP Networking**: Implements a text-based protocol over TCP for client-server communication
- **Basic Replication**: Forwards writes (`SET`, `DELETE`) from leader to follower nodes for data redundancy
- **CLI Client**: Command-line interface for easy interaction with the server
- **Persistence**: JSON-based data persistence with durability across server restarts
- **Graceful Shutdown**: Clean server shutdown with context-based cancellation
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

Puffin was built to achieve fluency in Go over a weekend, focusing on:
- Go syntax, error handling, and idiomatic patterns
- Concurrency with goroutines, channels, and `sync.Mutex`
- TCP networking using the `net` package
- Basic distributed systems concepts like leader-follower replication
- Standard library usage (`encoding/json`, `os`, `flag`, `log`)

This project aligns with low-level, networking, and distributed systems roles, showcasing practical skills in Go for building networked applications.

## 📋 Prerequisites

- **Go**: Version 1.18 or later ([download from go.dev](https://go.dev))
- **Tools**: Terminal and optional tools like `telnet` or `nc` (netcat) for manual testing
- **OS**: Tested on Linux/macOS; should work on Windows with minor adjustments

## 🚀 Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/puffin.git
cd puffin

# Install dependencies
go mod download

# Build binaries
make build
# Or manually:
# go build -o bin/puffin-server cmd/server/main.go
# go build -o bin/puffin-cli cmd/client/main.go
```

### Basic Usage

1. **Start the server:**
   ```bash
   ./bin/puffin-server
   # Server starts on :8080 by default
   ```

2. **Use the CLI client:**
   ```bash
   # Set a key-value pair
   ./bin/puffin-cli set mykey "Hello, Puffin!"
   
   # Get a value
   ./bin/puffin-cli get mykey
   # Output: Hello, Puffin!
   
   # Delete a key
   ./bin/puffin-cli delete mykey
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
./bin/puffin-server
```

Customize configuration:
```bash
./bin/puffin-server -port :8081 -data mystore.json -follower localhost:8082
```

### Running a Follower Server

Start a follower server on a different port:
```bash
./bin/puffin-server -port :8081 -data follower.json -mode follower
```

The leader automatically forwards `SET` and `DELETE` commands to configured followers.

### CLI Client Options

```bash
# Basic operations
./bin/puffin-cli -addr localhost:8080 -cmd set -key foo -value bar
./bin/puffin-cli -addr localhost:8080 -cmd get -key foo  
./bin/puffin-cli -addr localhost:8080 -cmd delete -key foo

# Batch operations
./bin/puffin-cli -addr localhost:8080 -file commands.txt

# JSON output for scripting
./bin/puffin-cli -addr localhost:8080 -cmd get -key foo -format json
```

### Configuration File

Create a `config.yaml` file for advanced configuration:
```yaml
server:
  port: ":8080"
  data_file: "puffin.json"
  
replication:
  mode: "leader"
  followers:
    - "localhost:8081"
    - "localhost:8082"

logging:
  level: "info"
  format: "json"

metrics:
  enabled: true
  port: ":9090"
```

Run with config:
```bash
./bin/puffin-server -config config.yaml
```

## 📊 Example Session

```bash
# Terminal 1: Start leader
$ ./bin/puffin-server
2025-01-15 10:30:00 INFO Server starting on :8080

# Terminal 2: Start follower  
$ ./bin/puffin-server -port :8081 -mode follower
2025-01-15 10:30:05 INFO Follower server starting on :8081

# Terminal 3: Use CLI client
$ ./bin/puffin-cli set greeting "Hello, distributed world!"
OK

$ ./bin/puffin-cli get greeting
Hello, distributed world!

$ ./bin/puffin-cli delete greeting
OK

# Verify replication worked
$ ./bin/puffin-cli -addr localhost:8081 get greeting
ERROR: Key not found (confirming deletion was replicated)
```

## 📁 Project Structure

```
puffin/
├── cmd/                    # Application entry points
│   ├── server/            # Server binary
│   ├── client/            # CLI client binary
│   └── cloud/             # Enterprise cloud version
├── internal/              # Private application code
│   ├── store/             # Core key-value store logic
│   ├── server/            # TCP server implementation  
│   ├── client/            # Client implementation
│   ├── config/            # Configuration management
│   └── metrics/           # Monitoring and metrics
├── pkg/                   # Public API packages
│   └── puffin/            # Public client library
├── enterprise/            # Enterprise features (commercial)
│   ├── monitoring/        # Advanced monitoring
│   ├── security/          # RBAC and encryption
│   ├── clustering/        # Raft consensus
│   └── backup/            # Backup and restore
├── docs/                  # Documentation
├── examples/              # Usage examples
├── deployments/           # Kubernetes, Docker configs
└── test/                  # Tests and benchmarks
```

## 🔧 Development

### Setup Development Environment

```bash
make setup-dev
```

### Run Tests

```bash
# Unit tests
make test

# Integration tests  
make test-integration

# Benchmarks
make bench

# Test coverage
make coverage
```

### Code Quality

```bash
# Linting
make lint

# Formatting
make fmt

# Security scan
make security
```

### Build and Release

```bash
# Build all binaries
make build

# Build for multiple platforms
make build-all

# Create release
make release VERSION=v1.0.0
```

## 🏗️ Architecture

Puffin follows a simple but effective architecture:

- **TCP Server**: Handles client connections with goroutines for concurrency
- **Key-Value Store**: Thread-safe in-memory storage with mutex protection
- **Replication**: Asynchronous leader-follower replication over TCP
- **Persistence**: JSON-based storage with atomic writes
- **Protocol**: Text-based command protocol for simplicity

For detailed architecture documentation, see [docs/architecture.md](docs/architecture.md).

## 📈 Performance

Basic performance characteristics (on a modern laptop):

- **Throughput**: ~50,000 operations/second (single node)
- **Latency**: <1ms average for local operations
- **Memory**: ~100MB for 1M key-value pairs
- **Persistence**: ~1000 writes/second to disk

Run benchmarks: `make bench`

## 🛡️ Security

### Open Source Security
- Basic input validation and sanitization
- Graceful error handling
- Memory-safe operations

### Enterprise Security (Commercial License)
- TLS encryption for all network communication
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
- **Performance**: Optimized storage backend and connection pooling
- **Security**: TLS encryption, authentication, and access control
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
4. **Test**: Ensure tests pass and add new tests for your changes
5. **Submit**: Create a pull request with a clear description

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
- [ ] HTTP API alongside TCP protocol
- [ ] WebAssembly client library  
- [ ] Improved CLI with shell completion
- [ ] Basic clustering documentation
- [ ] Performance optimizations

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
- **GitHub**: [https://github.com/yourusername/puffin](https://github.com/yourusername/puffin)
- **Docker Hub**: [https://hub.docker.com/r/puffindev/puffin](https://hub.docker.com/r/puffindev/puffin)

## 🙏 Acknowledgments

- **Inspiration**: Atlantic puffins' loyal pair-bonding, reflecting the paired (key-value) and cooperative (distributed) nature of this project
- **Learning**: Built as a portfolio piece for low-level, networking, and distributed systems roles
- **Community**: Thank you to all contributors, users, and supporters of the Puffin project

---

**Made with ❤️ and Go**
[Support](https://coindrop.to/timothyelems)

*Puffin: ...because kv paired databases don't exist yet.*
