# EmailIntel System Architecture

## Services Map

| # | Service | Port | Responsibility |
|---|---------|------|----------------|
| 1 | api-gateway | 8000 | Reverse proxy, JWT validation, rate limiting, request routing |
| 2 | auth-service | 8001 | Authentication, JWT issuance, MFA (TOTP), refresh-token lifecycle |
| 3 | tenant-service | 8002 | Multi-tenancy management, user RBAC, API key management |
| 4 | verification-service | 8003 | Single-email and batch verification (syntax, DNS, MX, SMTP, disposable checks) |
| 5 | bulk-service | 8004 | CSV upload intake, job queue management, progress tracking |
| 6 | worker-service | 8005 | Background workers that consume bulk jobs from the queue |
| 7 | analytics-service | 8006 | Aggregated metrics, time-series queries, CSV exports |
| 8 | webhook-service | 8007 | Webhook registration, HMAC signing, delivery with retry |
| 9 | audit-service | 8008 | Append-only audit log ingestion and querying |
| 10 | admin-service | 8009 | Super-admin panel: tenant management, fraud signals, platform stats |
| 11 | suppression-service | 8010 | Suppression list CRUD, bulk CSV import, real-time lookups |
| 12 | billing-service | 8011 | Subscription management, invoice retrieval, usage metering |
| 13 | notification-service | 8012 | Email/SMS notifications triggered by platform events |
| 14 | customer-frontend | 3000 | React SPA — customer dashboard, verification UI, analytics |
| 15 | admin-frontend | 3001 | React SPA — super-admin dashboard |
| 16 | infrastructure | — | PostgreSQL, Redis, MinIO, Prometheus, Grafana, Loki, Jaeger |

---

## Data Flow

```
Client (Browser / API Consumer)
        |
        v
  [ API Gateway :8000 ]
   - Validates Bearer JWT
   - Enforces per-tenant rate limits (Redis token bucket)
   - Routes request to target microservice
        |
   ┌────┴─────────────────────────────────────┐
   |  Microservice Layer (8001 – 8012)        |
   |  Each service owns its DB schema         |
   |  Inter-service calls via internal HTTP   |
   └────┬─────────────────────────────────────┘
        |
   ┌────┴────────┐      ┌──────────────┐
   | PostgreSQL  |      |    Redis     |
   | (primary    |      | - Session    |
   |  data store)|      | - Rate limits|
   └─────────────┘      | - Job queue  |
                        | - Cache      |
                        └──────────────┘

Bulk processing path:
  bulk-service --> Redis queue --> worker-service
  worker-service --> verification-service (internal)
  worker-service --> MinIO (result CSV storage)
  worker-service --> webhook-service (completion event)
```

### Request lifecycle (typical API call)

1. Client sends `Authorization: Bearer <JWT>` to `http://localhost:8000/api/...`
2. API Gateway verifies JWT signature and expiry; rejects with 401 on failure.
3. Gateway extracts `tenantId` and `userId` from JWT claims, injects them as `X-Tenant-Id` / `X-User-Id` headers.
4. Gateway checks rate limit bucket for the tenant in Redis; returns 429 if exhausted.
5. Request is proxied to the appropriate upstream microservice.
6. Microservice validates RBAC role from headers; processes business logic.
7. Microservice writes to PostgreSQL and/or publishes events to Redis pub/sub.
8. Audit-service consumes audit events asynchronously and stores them.
9. Response is returned through the gateway to the client.

---

## Database Design

All services share a single PostgreSQL instance in development; in production each service uses its own schema (schema-per-service pattern).

### Key tables

| Table | Schema / Service | Description |
|-------|-----------------|-------------|
| `tenants` | tenant | Tenant master record, plan, status |
| `users` | tenant | User accounts linked to a tenant |
| `api_keys` | tenant | Hashed API key credentials |
| `verification_results` | verification | Per-email check outcome, score, checks JSON |
| `bulk_jobs` | bulk | Job metadata and progress counters |
| `bulk_job_rows` | bulk | Individual email rows per job (partitioned) |
| `analytics_daily` | analytics | Pre-aggregated daily counters (materialised) |
| `webhooks` | webhook | Webhook endpoint config |
| `webhook_deliveries` | webhook | Delivery attempts with status codes |
| `audit_logs` | audit | Immutable action log |
| `suppression_list` | suppression | Suppressed email addresses |
| `subscriptions` | billing | Stripe subscription mirror |
| `invoices` | billing | Invoice records |

### Key relationships

- `tenants` 1 --- N `users`
- `tenants` 1 --- N `api_keys`
- `tenants` 1 --- N `verification_results`
- `tenants` 1 --- N `bulk_jobs`
- `bulk_jobs` 1 --- N `bulk_job_rows`
- `tenants` 1 --- N `webhooks`
- `webhooks` 1 --- N `webhook_deliveries`
- `users` 1 --- N `audit_logs`

---

## Security

### Zero-trust model

- No microservice trusts inbound traffic without explicit header validation.
- Internal service-to-service calls require a shared `X-Internal-Secret` header; exposed services cannot be called directly from outside the container network.
- Secrets (JWT secret, DB passwords, SMTP credentials) are injected via environment variables and never committed to source control.

### Authentication & authorisation

| Mechanism | Details |
|-----------|---------|
| JWT (RS256) | Short-lived access tokens (15 min) issued by auth-service |
| Refresh tokens | Long-lived (30 days), stored hashed in Redis, single-use rotation |
| API keys | SHA-256 hashed at rest; prefix stored in plain text for display |
| MFA | TOTP (RFC 6238) via authenticator apps; backup codes stored bcrypt-hashed |
| RBAC | Roles: `owner > admin > member > viewer`; enforced per route in each service |

### Data protection

- All data encrypted at rest (PostgreSQL `pgcrypto` + AES-256 volume encryption in production).
- TLS 1.2+ enforced on all external endpoints in production.
- PII fields (email addresses) subject to tenant data-residency policies.

---

## Observability

### Metrics — Prometheus + Grafana

- Each service exposes a `/metrics` endpoint in Prometheus format.
- Grafana (`:3002` in development) ships pre-built dashboards for:
  - Request rate, error rate, latency (RED method)
  - Verification throughput and result distribution
  - Queue depth and worker lag
  - Per-tenant quota utilisation

### Logging — Loki + Promtail

- Structured JSON logs written to stdout by all services.
- Promtail scrapes container stdout and ships to Loki.
- Grafana Explore provides ad-hoc log queries with LogQL.
- Log retention: 30 days in development, configurable in production.

### Distributed tracing — Jaeger

- All services instrument outgoing HTTP calls with OpenTelemetry.
- Trace context propagated via `traceparent` header (W3C).
- Jaeger UI (`:16686` in development) provides end-to-end trace visualisation.

### Alerting

- Prometheus Alertmanager fires alerts on:
  - Error rate > 1% over 5 minutes
  - P99 latency > 2 seconds
  - Worker queue depth > 10 000
  - Disk / memory pressure thresholds
