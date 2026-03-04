# System Design Questions for Senior .NET Developers

This document contains high-value system design interview questions tailored for senior .NET/backend candidates. Each question includes clarifying prompts, key areas to discuss, sample design directions, trade-offs, and follow-ups.

How to use:
- Ask the candidate the core question, then use the clarifying prompts to guide scope.
- Look for: clear requirements, appropriate abstractions, trade-off awareness, scalability, reliability, security, and operational concerns.

---

## 1) Design a URL Shortener (like bit.ly)

- Clarify: throughput, latency, analytics, custom aliases, expiry.
- Key areas: unique ID generation, storage model, redirect performance, analytics pipeline, collision handling, data retention.
- Sample approach: use base62 shortcodes from a central ID generator (e.g., sequence + salt) or hash with collision resolution; store mapping in a highly-available key-value store (e.g., Cosmos DB, Redis for hot items, backing SQL for cold storage); use CDN + edge redirects for low latency.
- Trade-offs: short vs. readable IDs, eventual consistency for analytics, single-sequence hotspot vs. distributed ID generation (Snowflake-like), eviction/backfill for Redis.
- Follow-ups: support custom domains, rate limiting, abuse detection, GDPR deletion.

## 2) Design an E-commerce Orders Service

- Clarify: monolith vs. microservices, transaction guarantees, payment integration, inventory consistency, expected QPS.
- Key areas: data model (orders, order_items), payment workflow, inventory reservations, idempotency, eventual consistency using events, scaling read vs write paths.
- Sample approach: use an Orders service that emits domain events (OrderPlaced, PaymentSucceeded); Inventory service subscribes and reserves stock; use a saga (or orchestration) for multi-step transactions; keep queries in a separate read model (CQRS) for fast product/order views.
- Trade-offs: immediate consistency for inventory vs. user experience; complexity of sagas; transactional outbox to avoid lost events.
- Follow-ups: handling partial failures, retries, refund flow, analytics, scaling spikes (Black Friday).

## 3) Design a Real-time Notification System (push to web/mobile)

- Clarify: per-user fanout vs. topic-based, delivery guarantees, multi-device, offline delivery, scale targets.
- Key areas: transport (WebSockets, SignalR, FCM/APNs), message routing, persistence for offline, backpressure, authentication, rate limiting.
- Sample approach: use SignalR for .NET server to manage connections, horizontally scale with Redis backplane or Azure SignalR Service; persist events to durable store for offline devices and replay; push notifications via FCM/APNs for mobile.
- Trade-offs: direct fanout costs vs. pull model; at-most-once vs. at-least-once; complexity of exactly-once.
- Follow-ups: delivery metrics, batching, deduplication, user presence, message ordering.

## 4) Design a Distributed Cache for .NET Web Apps

- Clarify: eviction policy, consistency model, failure modes, data hotness, size constraints.
- Key areas: cache-aside vs write-through, distributed store selection (Redis, NCache), sharding, replication, TTL strategies, cache stampede protection.
- Sample approach: use Redis cluster for shared cache; implement cache-aside with singleflight/mutex when populating; use versioned keys for invalidation; local in-process LRU for ultra-hot items.
- Trade-offs: complexity vs freshness, cache throughput vs consistency, cost of replication for availability.

## 5) Design a Rate Limiter for APIs

- Clarify: per-user vs per-API key vs global, burst allowance, sliding window vs token bucket, distributed environment.
- Key areas: rate-limiting algorithm, distributed enforcement, storage for counters (Redis INCR/Leaky bucket), fairness, handling clock skew.
- Sample approach: implement token bucket in Redis using Lua scripts for atomicity; provision local short-term caches for latency-sensitive checks; degrade gracefully when store unavailable.
- Trade-offs: precision vs performance, memory per key, potential for slight overage under high concurrency.

## 6) Design an Image Processing Pipeline

- Clarify: sync vs async upload processing, throughput, supported transformations, storage, CDN integration.
- Key areas: ingestion, job queue (Azure Queue/Service Bus/Kafka), worker pool, horizontal scaling, idempotency, storage (Blob Storage/S3), CDN caching, retries, backpressure.
- Sample approach: on upload, store raw in blob, enqueue processing job with needed transformations, workers read jobs, write outputs to blob, update metadata store, invalidate CDN on update.
- Trade-offs: synchronous upload user wait vs async; cost of multiple copies; handling large files and memory footprint.

## 7) Design a Search Service for Product Catalog

- Clarify: full-text vs filtered search, real-time indexing needs, multi-tenancy, ranking, facets.
- Key areas: index schema, search engine (Elasticsearch/Opensearch/Azure Cognitive Search), indexing pipeline, denormalization, query routing, caching hot queries.
- Sample approach: use near-real-time indexing via change feed; maintain denormalized documents for efficient queries; implement ranking and filtering with query-time boosts; tune analyzers for language-specific tokenization.
- Trade-offs: indexing latency vs fresh data, storage duplication, complexity of relevance tuning.

## 8) Design a Metrics & Observability Platform for .NET Microservices

- Clarify: types of telemetry (logs, metrics, traces), retention, alerting, SLOs.
- Key areas: instrumentation (OpenTelemetry), metric aggregation (Prometheus/Azure Monitor), distributed tracing (Jaeger/Application Insights), log centralization (ELK), dashboards, SLO-based alerting.
- Sample approach: instrument services with OpenTelemetry SDK for .NET, export traces/metrics to Application Insights or OTLP collector; store logs in Azure Log Analytics; define SLOs and configure alerting.
- Trade-offs: retention cost vs diagnostics, sampling rate for traces, overhead of tracing.

## 9) Design a Feature Flag System

- Clarify: targeting rules, SDKs, rollout strategy, auditing, performance constraints.
- Key areas: storage, evaluation engine, client-side vs server-side flags, rollout controls (percent rollouts), A/B experiments, audit logs, secure targeting.
- Sample approach: central flag service with a rules engine, local SDKs that cache flags with periodic refresh, support streaming updates for immediate rollout (e.g., SSE/SignalR), and use a datastore like Cosmos DB for durability.
- Trade-offs: consistency of flag evaluations, staleness vs availability, complexity of targeting rules.

## 10) Design a Logically Partitioned Relational DB for Multi-tenant SaaS

- Clarify: tenant isolation level (shared schema vs separate DB), tenant size variance, compliance requirements.
- Key areas: partitioning/sharding strategy, routing layer, metadata catalog, cross-tenant queries, migrations, backups.
- Sample approach: start with shared schema and tenant_id column with row-level security (RLS) for isolation; for large tenants, migrate to dedicated database; use a gateway service that routes queries to correct DB.
- Trade-offs: operational complexity vs cost, ease of tenant migration, backup/restore granularity.

## 11) Design an Event Sourcing System for Auditable Workflows

- Clarify: read model requirements, replay needs, snapshotting, expected event volume.
- Key areas: event store selection, idempotency, projection/update pipelines, snapshot strategy, versioning, debugging tools.
- Sample approach: use an append-only event store (EventStoreDB or using Kafka topics with compacted logs), project events to read models, implement snapshots every N events, and use an outbox pattern for external side-effects.
- Trade-offs: complexity vs auditability, cost of storing events, query patterns for read models.

## 12) Design a Global Leader Election System for Distributed Jobs

- Clarify: leader lifetime, failover time, consistency requirements, number of nodes.
- Key areas: election algorithm (Raft, ZooKeeper, Redis-based locks), lease semantics, clock skew handling, split-brain avoidance.
- Sample approach: use a consensus store (e.g., etcd/Consul) or implement leader lease with Redis using SETNX and expiry plus clocksync; monitor heartbeats and automatic re-election.
- Trade-offs: complexity of consensus protocols vs simplicity of lease-based leaders; safety windows during network partitions.

---

## Tips for Interviewers

- Start broad, then narrow: allow candidate to pick a high-level approach before diving into details.
- Probe trade-offs and alternatives: ask why they chose a component and what they'd change for different constraints.
- Evaluate operational thinking: logging, metrics, alerts, and runbook considerations.
- Watch for communication and prioritization: senior engineers should balance simplicity and correctness.

## Suggested follow-ups and resources

- Reference architectures: Azure Architecture Center, Microsoft docs for SignalR, Cosmos DB patterns.
- Patterns to know: CQRS, Event Sourcing, Saga, Circuit Breaker, Bulkhead, Backpressure.

---

Feel free to request changes (more questions, deeper sample answers, or a shorter interview-friendly list).
