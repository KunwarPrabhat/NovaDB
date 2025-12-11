# NovaDB — High-Performance In-Memory Database

**NovaDB** is a Redis-inspired, high-performance, in-memory key–value database built from scratch in C# using .NET.  
It demonstrates **systems-level engineering**, focusing on **speed, concurrency, clean architecture**, and **extensibility**.

---

## Key Features

- **Custom TCP Server**
  - Handles multiple concurrent client connections using asynchronous I/O.
  - Fully compatible with Redis-style RESP protocol.

- **RESP-Inspired Protocol**
  - Efficient parsing and serialization of commands and responses.
  - Supports bulk strings, simple strings, integers, and errors.

- **Command Layer**
  - Core commands: `SET`, `GET`, `DEL`, `EXPIRE`, `TTL`, `INFO`.
  - Extensible for adding new commands.
  - Handles validation, routing, and TTL checks.

- **High-Performance Storage Engine**
  - Thread-safe, sharded in-memory key–value store.
  - TTL/expiration management for automatic eviction.
  - Optimized memory usage to reduce garbage collection pressure.

- **Persistence**
  - Append-Only File (AOF) for durability.
  - RDB-style snapshotting for fast recovery.
  - Background persistence management to minimize write blocking.

- **Clean Architecture**
  - Layers: `Server → Command → Storage → Protocol/Network`.
  - Decoupled design for maintainability and extensibility.

- **Testing & Reliability**
  - Fully unit-tested core components.
  - Integration tests for persistence, commands, and networking.

---

## NovaDB Architecture Overview

+-----------------------------------------------------------+
|                       NovaDB.Server                       |
+-----------------------------------------------------------+
| - Entry point (Program.cs)                                |
| - Dependency Injection setup                               |
| - Configuration loading (NovaConfig.cs)                    |
| - Lifecycle management (startup/shutdown hooks)            |
+-----------------------------------------------------------+
                             |
                             ▼
+-----------------------------------------------------------+
|                     Command Layer                          |
+-----------------------------------------------------------+
| - Command Dispatcher                                       |
|     Maps command strings (SET, GET, DEL, etc.) → Handlers |
|                                                            |
| - Built-in Command Handlers                                |
|     SET, GET, DEL, EXPIRE, TTL, INFO, FLUSHDB              |
|                                                            |
| - Extensible interface for custom commands                 |
| - Performs validation, TTL checks, and command routing     |
+-----------------------------------------------------------+
                             |
                             ▼
+-----------------------------------------------------------+
|                    Storage Engine                          |
+-----------------------------------------------------------+
| - In-Memory Key-Value Store                                |
|     • Sharded store for concurrency                        |
|     • Thread-safe access (partition/RW locks)              |
|     • TTL/Expiration management                            |
|                                                            |
| - Data Structures                                          |
|     • Hash tables / Dictionaries                           |
|     • CacheEntry / ExpirationRecord                        |
|                                                            |
| - Persistence Layer                                        |
|     • Append-Only File (AOF) writer & reader               |
|     • Snapshotting (RDB-style)                             |
|     • Background persistence manager                       |
|                                                            |
| - APIs exposed to Command Layer                            |
|     Set / Get / Del / Expire / Scan / Flush               |
+-----------------------------------------------------------+
                             |
                             ▼
+-----------------------------------------------------------+
|                 Protocol + Network Layer                   |
+-----------------------------------------------------------+
| - TCP Server (async)                                       |
|     • Accepts multiple concurrent client connections       |
|     • Handles connection lifecycle                         |
|                                                            |
| - RESP-Inspired Protocol Parser                            |
|     • Parses raw bytes into structured commands            |
|     • Writes responses (bulk strings, ints, errors)        |
|                                                            |
| - Connection Management                                    |
|     • Tracks client state                                  |
|     • Authentication support (optional)                    |
|                                                            |
| - Network I/O Abstractions                                 |
|     • System.IO.Pipelines or raw sockets                   |
|                                                            |
| - Outputs decoded commands to Command Layer                |
+-----------------------------------------------------------+
