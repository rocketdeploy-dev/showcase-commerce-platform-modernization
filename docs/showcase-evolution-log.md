# Showcase Evolution Log

This document records major architectural changes reflected in this
public showcase repository.

It does **not** describe business outcomes or product evolution.

Its purpose is to track how the showcased codebase evolves as a
technical architecture artifact: which boundaries become more explicit,
which execution or integration patterns are formalized, and how the
repository increasingly represents a modernization-in-progress rather
than only a stabilized legacy baseline.

------------------------------------------------------------------------

## Entry 001 — Clearer module boundaries, formalized integration seams, and hardened runtime

### Summary

At this stage, the underlying application no longer reflects only the
characteristics of a stabilized legacy monolith.

The codebase now shows a more explicit architectural transition in
several important areas:

-   clearer module-oriented structure in selected domains,
-   more formalized integration access patterns,
-   raw-first ingestion and reprocessing in selected flows,
-   stronger operational control in cyclic/background execution,
-   clearer separation between durable artifacts and temporary local
    processing.

This milestone marks the point at which the showcase should represent
the system not only as a long-running modular monolith, but as a legacy
platform with **visible boundary-first modernization already implemented
in code**.

### Architectural signals now visible in the application

#### 1. Clearer module-oriented structure

Selected domains now expose more explicit internal module boundaries
rather than relying only on broad functional grouping inside a shared
monolith.

This does not yet create runtime isolation, but it does create clearer
ownership zones and more realistic extraction seams.

#### 2. Formalized integration seams in selected domains

Some integration-heavy paths now show more explicit separation between:

-   transport concerns,
-   application-layer orchestration,
-   provider-facing access logic.

This is a meaningful change from older legacy patterns where integration
calls were more diffusely embedded across controllers, models, and mixed
orchestration flows.

#### 3. Raw-first ingestion and reprocessing

Selected external-data flows now separate:

-   raw payload capture,
-   projection into internal state,
-   reprocess/replay capability.

This increases architectural maturity because it improves recovery,
replay, and diagnostics without requiring immediate service extraction.

#### 4. Hardened cyclic/background runtime

Background execution now shows stronger runtime control than a simple
legacy scheduler model.

Important characteristics at this stage include:

-   bounded execution,
-   lock-based coordination,
-   jittered scheduling behavior,
-   structured runtime signaling.

This matters because execution safety is being improved before any full
replacement with a dedicated worker platform.

#### 5. Clearer artifact durability direction

Generated artifacts are increasingly handled through more explicit
durability patterns rather than being treated as implicitly durable local
filesystem state.

This improves stateless-readiness and reduces architectural dependence
on node-local persistence assumptions.

### Why this milestone changes the represented architecture

Earlier versions of the showcase could accurately emphasize:

-   long-term production use,
-   monolithic structure,
-   stabilization work,
-   future modernization intent.

At this stage, that framing is no longer complete.

The repository now contains enough implemented architectural movement
that the showcase should also reflect:

-   clearer internal boundaries,
-   more explicit contracts in selected domains,
-   stronger runtime discipline,
-   more visible extraction-readiness.

The significance of this milestone is not architectural completion, but
the fact that the system now exposes **concrete transitional patterns**
rather than only potential future direction.

### Boundary maturity at this stage

This milestone does **not** imply that the system is already:

-   fully modularized,
-   uniformly refactored,
-   service-isolated,
-   free of legacy coupling.

Legacy and newer patterns still coexist.

What has changed is that selected modernization patterns are now
concrete enough to materially change what this showcase represents as a
technical architecture artifact.

------------------------------------------------------------------------


## Entry 002 — Contract-driven consumers, append-only domains, and AI capability seams

### Summary

The showcased architecture has moved beyond the first visible module-boundary cleanup. It now represents a broader modernization stage in which selected domains are exposed through explicit read APIs, append-only/current-state models, Core API facades, MCP tools, AI gateway capabilities, and dedicated runtime jobs.

The important change is not runtime microservice extraction yet. The important change is that new consumers are increasingly routed through contracts rather than legacy implementation details.

### Architectural signals now visible

#### 1. Activities as read-model and audit-log boundary

The activities area now shows a dedicated `activities/v1` read boundary with list, detail, and filter endpoints. The module includes explicit DTOs, seller access scoping, legacy payload decoding, read-model mapping, and a normalized activity type model.

The storage direction also changed materially. Activity reads moved from the old legacy repository to a projected read repository, and the `Activity` model now represents `activities_current` rather than the old mutable table. Update and delete behavior is blocked, and new records are written through an append-only store.

#### 2. Orders as external synchronization and read boundary

The orders area now represents a full external-data modernization path: provider ports and DTOs, sync run tracking, raw provider payload storage, snapshots, line items, external references, change/payload hashing, current-state read models, internal legacy read API, Core API facade, MCP tools, and a dedicated external orders sync job container.

The eBay path also includes fulfillment-oriented data such as ship-by dates and shipment urgency semantics.

#### 3. Messages as multilingual and AI-assisted workflow boundary

The messages area now includes thread synchronization, message reads, translation, reply assistance, and send-reply flows behind explicit use cases and v1 endpoints.

Implemented architectural elements include:

- thread and thread-list translation use cases,
- translation DTOs,
- translation endpoints for selected thread lists and individual threads,
- persisted per-user translation language preference,
- UI integration in `messages-ui.js`,
- AI gateway translation adapter,
- message translation worker use case,
- dedicated message translation worker container,
- reply composer context endpoint,
- suggested reply draft endpoint,
- compose-reply endpoint,
- final send-reply endpoint kept as a separate explicit action.

#### 4. Notifications as user-scoped MCP tools

Notifications existed in an earlier modernization stage. The newer architectural change is tool exposure. Notifications are now available through Core API and MCP tools for list, detail, mark-read, archive, and bulk operations.

The MCP/Core path propagates trusted internal user context using `X-Internal-User-Id`.

#### 5. MCP as a controlled AI tool boundary

The MCP server now exposes real Comers domain tools for messages, notifications, and orders. These tools route through `comers-core-api`; they do not call the legacy application directly.

For sensitive user-scoped domains, tools fail closed when trusted internal-user context is missing.

```text
ChatKit / AI runtime
→ MCP
→ Core API
→ legacy internal APIs and modules
```

#### 6. AI gateway as capability boundary

The AI gateway now represents a separate internal capability layer. It exposes Comers-owned contracts such as `translate.v1` and `compose_reply.v1`.

The gateway owns provider integration, structured OpenAI Responses API output, persistence for capability operations, idempotent operation creation, async operation reads, and background polling. The full AI runtime includes a dedicated gateway poller container.

#### 7. Runtime repositories as part of the architecture

Production/runtime Compose definitions were moved out of service repositories into dedicated infrastructure repositories. The AI runtime stack includes webapp, BFF, MCP, gateway, gateway poller, and ChatKit runtime. The legacy runtime stack is expressed as app, web, and multiple job containers, including external orders sync, message thread sync, recent message sync, and message translation worker.

### Why this milestone changes the showcase

The showcase no longer represents only a modularizing monolith. It now also represents contract-first access for new consumers, append-only/current-state domain modeling, AI-facing tool boundaries, internal AI capability contracts, and explicit runtime/job ownership.

Legacy and modernized patterns still coexist, but the repository now shows concrete seams through which future service extraction and AI-assisted operations can continue without bypassing the system architecture.

### Entry 002 technical implementation details

This addendum keeps the showcase log focused on technical architecture rather than business outcomes. It records concrete seams that should be visible to reviewers when comparing this showcase with the earlier baseline:

- `activities/v1` routes for list, detail, and filter access,
- activity DTOs, read-model mapping, seller access scoping, and legacy payload decoding,
- `activities_current` projection usage and disabled update/delete semantics for activity records,
- append-only external order synchronization with raw payloads, snapshots, external references, sync runs, and current-state reads,
- `orders/v1/items` read API and a Core API `OrdersModule`,
- MCP tools for messages, notifications, and orders routed through Core API,
- fail-closed trusted internal-user context for scoped tools,
- messages translation use cases, translation DTOs, user language preference, UI integration, and translation worker runtime,
- reply composer context, suggested reply draft, and compose reply use cases,
- gateway capabilities `translate.v1` and `compose_reply.v1`,
- gateway operation persistence, idempotent create behavior, and gateway poller runtime,
- dedicated infra repositories for AI and legacy runtime composition.

------------------------------------------------------------------------
Future entries should be added only for major architectural milestones
that materially change what this showcase represents.
