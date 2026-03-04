# System Design Questions for Senior .NET Developer

নিচে কয়েকটি সাধারণ সিস্টেম ডিজাইন প্রশ্ন এবং এগুলোর সংক্ষিপ্ত বাংলা উত্তর রয়েছে — সিংহভাগ উত্তর সিনিয়র .NET ডেভেলপার হিসেবে আলোচনা ও সিদ্ধান্ত গ্রহণের জন্য নির্দেশিকা হিসাবে লেখা।

---

## 1) একটি সিস্টেম যেখানে লাখো রিকোয়েস্ট/সেকেন্ড হ্যান্ডেল করতে হবে — আপনি .NET স্ট্যাক দিয়ে কীভাবে স্কেল করবেন?

- **উত্তর:** প্রথমে সার্ভারের গতির জন্য `ASP.NET Core` + `Kestrel` ব্যবহার করে CPU-bound কাজগুলো অপ্টিমাইজ করি। Stateless সার্ভার রাখি যাতে auto-scaling সহজ হয় (Docker + Kubernetes)। সেশন স্টোর হলে Redis ব্যবহার করে externalize করি। লোড-ব্যালান্সিং এর জন্য Kubernetes Service / cloud LB এবং CDN (স্ট্যাটিক কনটেন্ট)। ব্যাচ প্রক্রিয়াকরণের জন্য background workers (e.g., `IHostedService`, Hangfire, or Azure Functions) এবং asynchronous I/O + connection pooling নিশ্চিত করি।

## 2) ডেটা শার্ডিং/পার্টিশনিং কবে ও কীভাবে করবেন?

- **উত্তর:** যদি এক টেবিলে রেকর্ড খুব বড় হয়ে পড়ে এবং query latency বাড়ে, তখন পার্টিশনিং দরকার। শার্ডিং কৌশল: range-based (সময় বা id range), hash-based (uniform distribution), বা directory-based (routing layer)। .NET অ্যাপ্লিকেশনে shard-router ব্যবহার করে connection string/runtime routing করি অথবা middleware/ORM স্তরে (EF Core) নিজের routing লেয়ার লিখি। টেইনিং: shard rebalancing কানে ফেলতে হবে—এবং cross-shard joins জটিল, তাই চেষ্টা করব denormalize বা aggregate পরামর্শে।

## 3) Read-heavy সিস্টেমে ল্যাটেন্সি কমাতে আপনি কী করবেন?

- **উত্তর:** read replicas ব্যবহার করব (DB রেপ্লিকা) এবং caching (Redis/Memcached) দিয়ে hot data serve করব। CDN টা স্ট্যাটিক ও কনটেন্ট-ইনটেন্সিভ রিসোর্সে লাগাব। CQRS ব্যবহার করে read path আলাদা করে optimize করব—read model denormalized রাখব। ক্যাশ ইনভ্যালিডেশন নীতিটি পরিষ্কার রাখতে হবে (TTL, cache-aside, write-through, বা invalidation messages)।

## 4) Consistency vs Availability — আপনি .NET সার্ভিসে কীভাবে সিদ্ধান্ত নেবেন?

- **উত্তর:** ব্যবসায়িক চাহিদা বিবেচ্য: অর্থ লেনদেন হলে strong consistency (ACID) প্রয়োজন—ট্রানজেকশনাল DB বা distributed transaction (সাবধানে)। user-feed বা analytics হলে eventual consistency গ্রহণযোগ্য। বাস্তবে হাইব্রিড কৌশল নেই: critical operations strong consistency, non-critical eventual। .NET-এ distributed consistency চাইলে patterns: Sagas (orchestration/choreography) for long-running transactions, idempotency tokens, retries with exponential backoff।

## 5) Sagas vs 2PC — কখন কোনটা ব্যবহার করবেন?

- **উত্তর:** 2PC (two-phase commit) রুখে রাখি কারণ এটি availability ও scalability খরচ বাড়ায়। মাইক্রোসার্ভিস আর্কিটেকচারে সাধারণত Sagas (orchestration or choreography) বেছে নেই—সার্ভিসগুলোর মধ্যে loosely coupled transaction manage করতে ভালো। .NET এ Sagas বাস্তবায়নের জন্য workflow orchestrator (e.g., MassTransit, NServiceBus, or custom orchestrator) ব্যবহার করা যায়।

## 6) High throughput messaging চান—কোনটি বেছে নেবেন: Kafka, RabbitMQ, Azure Service Bus? কেন?

- **উত্তর:** যদি ordered, partitioned, high-throughput log-like stream দরকার (event sourcing, analytics), Kafka বেছে নেব। যদি complex routing, RPC-like patterns বা lower throughput কিন্তু richer features চান, RabbitMQ ভালো। Azure-managed পরিবেশে হলে Azure Service Bus সুবিধাজনক (sessions, dead-lettering, managed infra)। .NET ইকোসিস্টেমে প্রত্যেকটির ক্লায়েন্ট লাইব্রেরি (Confluent.Kafka, RabbitMQ.Client, Azure.Messaging.ServiceBus) ভালো সাপোর্ট করে।

## 7) Database নির্বাচন — আপনি SQL vs NoSQL কখন বেছে নেবেন?

- **উত্তর:** relational integrity, complex joins, transactional consistency প্রয়োজন হলে SQL (e.g., SQL Server, Postgres)। উচ্চ write/read scale বা flexible schema লাগলে NoSQL (e.g., Cosmos DB for global distribution, MongoDB for document use-cases)। .NET এর EF Core SQL এ ভাল অভিজ্ঞতা দেয়; NoSQL হলে provider-specific SDK দিয়ে কাজ হয়।

## 8) Observability (logging, metrics, traces) আপনি .NET সার্ভিসে কীভাবে সাজাবেন?

- **উত্তর:** centralized logging (ELK/EFK or Azure Monitor), metrics (Prometheus + Grafana), distributed tracing (OpenTelemetry + Jaeger/Zipkin)। ASP.NET Core middleware এ correlation id, health checks (`Microsoft.Extensions.Diagnostics.HealthChecks`), structured logging (Serilog/Seq) এবং metrics exporters ব্যবহার করব। Telemetry সব সার্ভিসে consistent format ও sampling নীতি দরকার।

## 9) Resilience: retries, circuit breakers, bulkheads — .NET এ কিভাবে প্রয়োগ করবেন?

- **উত্তর:** Polly লাইব্রেরি .NET-এ retry, circuit-breaker, timeout, bulkhead policies এর জন্য industry standard। HttpClientFactory দিয়ে typed clients + policies attach করে resilient HTTP calls করি। resource isolation ও rate-limiting (quota) দিয়ে bulkheads বাস্তবায়ন করতে পারি।

## 10) Large file uploads/downloads — efficient design কী হবে?

- **উত্তর:** ক্লায়েন্ট-টু-cloud storage direct upload (pre-signed URLs / SAS tokens) দেয়া ভাল—ASP.NET সার্ভিস দ্বারা proxy না করে। যদি server-needed processing থাকে, streaming APIs ব্যবহার করে (IFormFile stream) এবং chunked uploads + resume support থাকলে ভালো। CDN দিয়ে large download serve করব।

## 11) Zero-downtime deploys — .NET অ্যাপে কিভাবে করবেন?

- **উত্তর:** rolling updates বা blue-green deployments (Kubernetes, Azure App Service deployment slots) ব্যবহার করব। DB migrations যতটা সম্ভব backward-compatible রাখতে হবে (expand-then-contract strategy)। health probes ও readiness checks রেখে new pod/service only receive traffic after ready হওয়া নিশ্চিত করব।

## 12) Authentication & Authorization — enterprise-grade .NET সিস্টেমে best practices?

- **উত্তর:** centralized identity provider (IdentityServer / Azure AD / ADFS) ব্যবহার করে OAuth2/OpenID Connect। ত্রুটি হলে token validation middleware (`JwtBearer`) ব্যবহার ও scopes/roles-based authorization enforce করব। sensitive config secrets secret store (Azure Key Vault) এ রাখব।

## 13) Event sourcing ব্যবহার করলে কী সমস্যা আসতে পারে এবং .NET-এ কী টুল ব্যবহার করবেন?

- **উত্তর:** challenges: event schema evolution, storage growth, rebuilding read models (replay cost), debugging complexity। .NET-এ event stores: EventStoreDB, or using Kafka as event log. projections/readonly views build করতে background processors লাগে। snapshotting এবং versioned events ব্যবহার করে mitigation করা যায়।

## 14) Caching strategy উদাহরণ দিন (consistency, invalidation) — .NET কন্টেক্সটে

- **উত্তর:** সাধারণভাবে cache-aside (অ্যাপ্লিকেশন রাহে cache fill/evict) সবচেয়ে সহজ। write-heavy scenarios এ write-through বা write-behind consider করতে পারি। distributed cache (Redis) ব্যবহার করলে invalidation messaging (pub/sub) অথবা cache key versioning/TTL প্রয়োগ করতে হবে। EF Core-level caching হলে query caching সতর্কতার সাথে ব্যবহার।

## 15) Scalability-related Bottlenecks শনাক্ত করতে আপনি কী metrics দেখবেন?

- **উত্তর:** latency-percentiles (p50/p95/p99), CPU, memory, GC metrics (Gen0/1/2 collections), thread pool starvation, DB query latencies, connection pool exhaustion, queue lengths, error rates, retries. .NET-specific: large object heap growth, blocking synchronous I/O on thread pool threads।

---

ফাইলটি আরও প্রসারিত করতে চাইলে বলুন—আমি প্রতিটি প্রশ্নের উপর .NET-নির্দিষ্ট কোড স্নিপেট, ডায়াগ্রাম বা কী প্রশ্ন ইন্টারভিউতে জিজ্ঞাসা করা হবে সেগুলোও যোগ করে দিতে পারি।
