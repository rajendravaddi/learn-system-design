# System Design Roadmap — Project-First Track


## Phase 0 — Pre-requisites
**Concepts:**
- Core programming fluency (functions, loops, recursion, file I/O) in your chosen language
- Data Structures: arrays, linked lists, stacks, queues, hashmaps, trees, heaps
- Time/space complexity (Big-O) — enough to reason about "is this fast enough"
- Git & GitHub: branches, commits, PRs, resolving merge conflicts

**Depth target:** Comfortable writing a 100+ line program without hand-holding, and comfortable pushing/branching in Git without looking up commands.

**Project:** A small CLI tool (e.g., a to-do list or expense tracker) using at least 3 data structures above, version-controlled from day one with proper commits.

---

## Phase 1 — OOP Foundations
**Concepts (learn to the point of implementing each, not just defining):**
- Classes & Objects
- Encapsulation
- Abstraction
- Inheritance (single, multiple, when to avoid it)
- Polymorphism (compile-time vs runtime)
- Composition vs Inheritance (know when to pick which)
- Interfaces / Abstract classes
- Dependency Injection (constructor injection at minimum)
- Custom Exceptions & exception hierarchies
- Generics / Type hints

**Depth target:** Be able to build a multi-class system from scratch (not tutorials) using at least 6 of the above together correctly.

**Project:** Library Management System (Book, Member, Librarian, borrow/return, custom exceptions)

---

## Phase 2 — Clean Code & Testing Basics
**Concepts:**
- Naming, function size, single-purpose functions, avoiding deep nesting
- Comments: when they help vs when they mask bad naming
- Unit testing fundamentals (arrange-act-assert, test isolation)
- Mocking/stubbing dependencies (directly sets up Dependency Injection from Phase 1 / DIP from Phase 2)
- Basic TDD loop (red-green-refactor) — practice, not mastery

**Depth target:** Be able to write unit tests for a class with an injected dependency, using a mock in place of the real one.

**Project:** Add a unit test suite (with mocks) to the Library Management System from Phase 1.

---

## Phase 3 — SOLID Principles
**Concepts:**
- Single Responsibility — one reason to change
- Open/Closed — extend without modifying
- Liskov Substitution — subtypes must be substitutable
- Interface Segregation — no fat interfaces
- Dependency Inversion — depend on abstractions, not concretions

**Depth target:** Be able to *spot* a SOLID violation in existing code and refactor it — not just recite the definitions.

**Project:** Refactor the Library System from Phase 1 to fix 2–3 SOLID violations. Build a Notification Sender (Email/SMS/Push) badly first, then refactor with DIP + interfaces.

---

## Phase 4 — Design Patterns
**Concepts (implement each standalone, 20–40 lines, before combining):**
- Creational: Factory, Builder
- Behavioral: Strategy, Observer, Command, State, Template Method, Chain of Responsibility
- Structural: Adapter, Decorator, Facade, Proxy

**Depth target:** For each pattern, know the *problem it solves*, a real-world trigger phrase (e.g. "notify multiple subscribers" → Observer), and be able to code it without reference.

**Project:** Food Ordering CLI App combining at least 5 patterns together (Strategy for payment, Observer for order status, Factory for order types, Decorator for add-ons, State for order lifecycle).

---

## Phase 5 — Concurrency & Multithreading Basics
**Concepts:**
- Threads vs processes (conceptual)
- Race conditions, critical sections
- Locks/Mutexes, semaphores (basic use, not internals)
- Thread-safe Singleton
- Producer-consumer pattern
- Deadlock: what causes it, how to avoid it

**Depth target:** Be able to identify where a shared resource in an LLD problem needs synchronization, and implement a thread-safe version of it.

**Project:** Make the Food Ordering App's order queue thread-safe (multiple "kitchens" consuming from one order queue).

---

## Phase 6 — Low-Level Design (LLD)
**Process to follow for every problem:**
```
Requirements → Use Cases → Entities → Classes → Interfaces → Relationships → Patterns Used → SOLID Check → Class Diagram → Code
```

**Concepts (added):**
- **UML basics :** class diagram notation (association, aggregation, composition, inheritance arrows), and enough sequence diagram notation to show one flow (e.g., "place order")

**Problems (pick 6–8 total across tiers, not all):**
- Beginner: Parking Lot, Tic-Tac-Toe, Snake & Ladder, Vending Machine
- Intermediate: Elevator System, Movie Ticket Booking, ATM, Splitwise
- Advanced: Food Delivery, Ride Sharing (LLD only, not distributed)

**Depth target:** Given any new, unseen LLD prompt, you should instinctively run through the process above without needing to look it up — including sketching the class diagram by hand.

---

## Phase 7 — Networking, APIs & Databases
**Concepts:**
- Client/server model
- REST principles, HTTP methods, status codes, idempotent vs non-idempotent
- **API documentation & versioning (OpenAPI/Swagger, URL vs header versioning) [NEW]**
- SQL: normalization, indexing, joins, transactions
- NoSQL: document/key-value/columnar — when each fits
- Basic replication (leader-follower) and basic sharding

**Depth target:** Be able to design a schema for a given problem, justify SQL vs NoSQL for it, and produce an OpenAPI spec for your endpoints.

**Project:** Turn an existing LLD project (e.g. Splitwise) into a real REST API with FastAPI/Flask + PostgreSQL, documented with Swagger.

---

## Phase 8 — Security Fundamentals
**Concepts:**
- Authentication vs Authorization
- Password hashing (bcrypt/argon2) — never store plaintext
- Session-based auth vs Token-based auth (JWT)
- OAuth2 basics (conceptual — what "login with Google" actually does)
- HTTPS/TLS — what it protects against
- Basic OWASP Top 10 awareness (SQL injection, XSS, CSRF — recognize, not deep-dive)

**Depth target:** Be able to add JWT-based auth to a REST API and explain what each layer protects against.

**Project:** Add authentication + role-based authorization (e.g., "admin" vs "member") to your Phase 5 API.

---

## Phase 9 — HLD Fundamentals
**Concepts:**
- API Gateways
- Reverse Proxies
- Load Balancers (round robin, least connections, consistent hashing basics)
- Horizontal vs Vertical Scaling
- Caching (cache-aside, write-through, TTL, eviction policies) — Redis
- CDN
- Message Queues (Kafka/RabbitMQ basics)
- Pub/Sub
- Rate Limiting (token bucket, sliding window)
- Consistent Hashing

**Depth target:** Be able to draw and justify a component-level architecture diagram for a mid-complexity system.

**Project:** Add caching + rate limiting to your Phase 5 API. Diagram a URL Shortener end-to-end.

---

## Phase 10 — Distributed Systems
**Concepts:**
- CAP Theorem (and where real systems sit on it)
- Strong vs Eventual Consistency
- Leader Election
- Consensus (Raft/Paxos — conceptual understanding, not implementation)
- Distributed Transactions (2PC, Sagas)
- Idempotency
- Fault Tolerance: Retries, Circuit Breakers, Backpressure
- At-least-once vs Exactly-once vs At-most-once processing

**Depth target:** For any HLD case study, be able to explain which of these concepts apply and why — this phase feeds directly into Phase 8, it doesn't stand alone.

---

## Phase 11 — HLD Case Studies
**Process for every system:**
```
Requirements → Capacity Estimation → APIs → Data Model → High-Level Architecture → DB Choice → Caching → Scaling → Failure Handling → Bottlenecks → Trade-offs
```

**Problems (pick 6–8, ordered easy → hard):**
1. URL Shortener
2. Pastebin
3. Twitter (feed generation, fan-out)
4. Instagram (media storage, CDN)
5. Notification System
6. Web Crawler
7. Uber (geo-indexing + real-time)
8. WhatsApp (messaging + presence)

**Depth target:** Be able to whiteboard any of these out loud, unaided, covering all 10 steps above.

---

## Phase 12 — Production & Deployment
**Concepts:**
- Docker (images, containers, Dockerfile basics)
- CI/CD (GitHub Actions basics) — **run your Phase 1.5 unit tests as a pipeline step [NEW]**
- Cloud deployment (any one provider, free tier is fine)
- Secrets management (env vars, never commit credentials) **[NEW]**
- Logging, basic metrics, basic monitoring/alerting
- SLO/SLA basics

**Depth target:** Be able to take a working API from local machine to a publicly accessible, monitored deployment with tests running automatically on every push.

**Project:** Dockerize + deploy your Phase 5/6 API with CI/CD (including automated tests) and basic monitoring.

---

## Phase 13 — AI System Design (optional, learn as-needed)
**Concepts:**
- Model Serving (batching, quantization, GPU scaling basics)
- Model Caching
- Load Balancing for inference
- RAG (retrieval-augmented generation)
- Embeddings & Vector Search / Vector DBs
- Data Pipelines for ML (Kafka/queues)
- Feature Stores
- LLM Gateways

**Depth target:** Be able to wrap a model behind an API, add caching/batching, and build a basic RAG pipeline over your own data.

**Project:** Small FastAPI wrapper around a HuggingFace model → extend into a basic RAG chatbot over your notes.

---