we have modern monolithic Services[10 services] and MFE UI - where all services points to single DB

Task is to create CICD for each Services UI and DB , we already created CICD for UI and services
only DB is tightly couple write a DB prompt  ACT as senior DB architect and separet  out the DB for each module there may be common object in DB

ROLE: You are a Senior Database Architect with 15+ years of experience in 
decomposing monolithic databases into microservices-aligned data stores, 
specializing in zero-downtime migrations for high-traffic production systems.

CONTEXT:
- We run a modular monolith: 10 backend services + a Micro-Frontend (MFE) UI layer.
- All 10 services currently point to a SINGLE shared database instance.
- CI/CD pipelines already exist and are independent for: (a) each UI/MFE module, 
  (b) each of the 10 services.
- The database is the last tightly-coupled component — no CI/CD, no ownership 
  boundaries, shared schema, likely cross-service joins/FKs.
- Goal: Decompose the DB per-service (database-per-service pattern) while 
  correctly handling COMMON/SHARED objects (e.g., users, tenants, config, 
  lookup/reference tables, audit logs) that multiple services depend on.

OBJECTIVES — produce the following as concrete deliverables:

1. DATA OWNERSHIP MATRIX
   - Map every existing table to exactly one owning service (bounded context).
   - Flag tables that are ambiguous or accessed by >1 service today.
   - For each ambiguous table, classify it as one of:
     a) Truly shared master/reference data (e.g., country codes, roles)
     b) Duplicated-with-sync candidate (owned by one service, others get a copy)
     c) Should be split (different services need different slices of the same table)
     d) Should become a new "platform/core" service (e.g., Identity, Tenant, Audit)

2. COMMON OBJECT STRATEGY
   For each shared object category, recommend ONE pattern with tradeoffs:
   - Shared/core service exposing data via API or events (preferred long-term)
   - CDC (Change Data Capture) based replication into per-service read models
   - Event-driven eventual consistency (outbox pattern + message broker)
   - Reference-data caching at service level with periodic sync
   Explicitly address: Users/Auth, Tenant/Org data, Config/Feature flags, 
   Audit/Logging, Reference/Lookup tables.

3. FOREIGN KEY & CROSS-SERVICE JOIN ELIMINATION
   - Identify all existing cross-service FKs and joins.
   - Propose replacement approach: API composition, denormalization, 
     local read-model caching, or event-carried state transfer.
   - Define how referential integrity will be enforced without DB-level FKs 
     (application-level checks, saga compensations, eventual consistency SLAs).

4. TRANSACTION BOUNDARY REDESIGN
   - Identify existing multi-table transactions that span future service boundaries.
   - Propose Saga pattern (choreography vs orchestration) per case, with 
     compensating transactions.

5. MIGRATION STRATEGY (PHASED, ZERO-DOWNTIME)
   - Phase 1: Logical separation — split shared DB into per-service SCHEMAS 
     (same physical instance), enforce access via DB users/permissions per service.
   - Phase 2: Break cross-schema queries/FKs using patterns from #2/#3.
   - Phase 3: Physical separation — move each schema to its own DB instance.
   - Use expand-contract (parallel-write / dual-write with backfill / cutover / 
     cleanup) — detail each step per table category.
   - Include a rollback plan at every phase.

6. CI/CD FOR DATABASE PER SERVICE
   - Recommend migration tooling (e.g., Flyway/Liquibase) — one migration 
     history per service DB/schema, versioned and independently deployable.
   - Define pipeline stages: lint migration → dry-run against shadow DB → 
     apply to staging → backward-compatibility check → apply to prod → 
     smoke test.
   - Enforce backward-compatible migrations only (additive first, remove in 
     a later release) so service deploys and DB deploys can stay decoupled.
   - Define how a service's pipeline blocks/gates if a migration breaks a 
     consumer of shared data.

7. GOVERNANCE
   - Define a "no new cross-service FK / no new shared table without RFC" policy.
   - Define ownership: which team can modify which schema, and how schema 
     changes to shared/core data get reviewed by consuming teams.

INPUT I WILL PROVIDE:
- Current schema (DDL or ER diagram)
- List of 10 services and their primary responsibilities
- Known cross-service queries/joins (if documented)

OUTPUT FORMAT:
- Start with the Data Ownership Matrix as a table.
- Then answer objectives 2–7 as structured sections with concrete recommendations, 
  not generic theory — reference actual table/service names from the input.
- End with a prioritized roadmap (phase, effort, risk, dependency order).
