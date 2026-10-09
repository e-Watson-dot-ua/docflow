# DocFlow – CLAUDE.md

## Project Context

**DocFlow** is a multi-tenant document management SaaS platform built as a pet project to gain hands-on **Azure cloud skills** with a modern C#/.NET stack.

**User:** Eduard — experienced .NET/C# backend developer, currently in military service. Daily duties give no exposure to cloud platforms, so this project is the way to build real Azure experience. Azure and other cloud services are used for personal learning only — never in connection with military duties. Preparing for AZ-900. Prefers direct/pragmatic communication; English is not his first language — keep wording simple and clear.

## Purpose & Goals

1. **Real Azure hands-on experience** — Provision, deploy, configure, secure, and monitor real Azure services, not local imitations
2. **Modern C#/.NET stack** — Use current .NET (10 LTS), .NET Aspire, minimal APIs, OpenTelemetry, and current best practices
3. **Cloud architecture patterns** — Multi-tenancy, event-driven messaging, CQRS, API gateway, Infrastructure as Code, CI/CD
4. **AZ-900 support** — Every Azure concept used in code maps to exam topics (compute, storage, networking, identity, cost management, governance)
5. **Portfolio project** — Public repository proving cloud-ready .NET skills to employers
6. **Future: SDK monetization** — Possible later phase offering a developer SDK on top of DocFlow

## Key Constraints

- **Cloud for learning only** — Personal Azure subscription; fully separate from military work (no work data, accounts, devices, or systems)
- **Budget: $10/month** — Prefer free tiers and consumption plans; Azure budget alert at $10 (alerts at 50%/80%/100%). Resources must be easy to tear down (`azd down`) and recreate — tear down after each study session, since Service Bus Standard (~$10/mo) and Container Registry Basic (~$5/mo) bill hourly/daily even when idle
- **Local-first development** — Daily coding runs locally via .NET Aspire + emulators/containers; Azure is the deployment target
- **Time-limited** — ~10 hours/week; ~6–8 week timeline
- **Learning-focused** — Understanding and clean pattern demonstration matter more than feature completeness

## Tech Stack

| Component | Technology | Azure Service (deployed) | Local Dev |
|-----------|-----------|--------------------------|-----------|
| **Language/Framework** | C# / .NET 10, ASP.NET Core minimal APIs | — | — |
| **Orchestration** | .NET Aspire | Azure Container Apps (via `azd`) | Aspire AppHost |
| **API Gateway** | YARP | Container App (ingress) | Aspire |
| **Database** | PostgreSQL + EF Core | Azure Database for PostgreSQL – Flexible Server | Container |
| **File storage** | Azure.Storage.Blobs | Azure Blob Storage | Azurite emulator |
| **Messaging** | MassTransit | Azure Service Bus | Service Bus emulator or RabbitMQ |
| **Real-time** | SignalR | Azure SignalR Service (free tier) | In-process SignalR |
| **Identity** | JWT / OpenID Connect | Microsoft Entra ID (or Entra External ID) | Dev JWT issuer |
| **Secrets** | Configuration + Managed Identity | Azure Key Vault | User secrets |
| **Observability** | OpenTelemetry + Serilog | Application Insights / Azure Monitor | Aspire Dashboard |
| **IaC** | Bicep | Azure Resource Manager | — |
| **CI/CD** | GitHub Actions + `azd` | Deployment via OIDC (no stored secrets) | — |
| **Architecture** | Clean Architecture + CQRS | — | — |

## Azure Skills Covered

| Area | What DocFlow Does | AZ-900 Topic |
|------|-------------------|--------------|
| Compute | Run API + worker in Container Apps | Compute & containers |
| Storage | Documents in Blob Storage, SAS tokens, access tiers | Storage services |
| Databases | Managed PostgreSQL, firewall rules, backups | Database services |
| Messaging | Service Bus topics/subscriptions, dead-lettering | Integration services |
| Identity | Entra ID auth, Managed Identity, RBAC | Identity, access & security |
| Secrets | Key Vault references | Security |
| Monitoring | Application Insights, Log Analytics queries (KQL), alerts | Monitoring tools |
| Governance | Resource groups, tags, budgets, cost analysis | Cost management & governance |
| IaC / DevOps | Bicep, `azd`, GitHub Actions | Deployment tools (ARM) |

## Architecture Notes

- **CQRS ≠ event sourcing.** Use CQRS (separate command/query handlers) with normal state-based storage. Domain events are published to Service Bus. Full event sourcing is optional, not a goal.
- **Modular monolith first, then extract a microservice.** Start with one API (modules: Organizations, Users, Documents) + one background Worker. Modules communicate only through public contracts/events, never through each other's tables — so extraction is possible later. In Phase 4, extract the Documents module into a separate microservice (own database, own deployment, Service Bus communication, YARP routing). This practices microservices without paying their cost from day one.
- **Multi-tenancy:** shared database with `TenantId` column + EF Core global query filters; tenant taken from JWT claims.

## Project Structure (Planned)

```
docflow/
├── CLAUDE.md
├── README.md                       # Overview, setup, architecture diagram
├── azure.yaml                      # azd configuration
├── infra/                          # Bicep templates
├── docs/
│   ├── ARCHITECTURE.md
│   └── adr/                        # Architecture decision records
├── src/
│   ├── DocFlow.AppHost/            # .NET Aspire orchestration
│   ├── DocFlow.ServiceDefaults/    # OpenTelemetry, health checks, resilience
│   ├── DocFlow.Gateway/            # YARP API gateway
│   ├── DocFlow.Api/                # Modular API (Organizations, Users, Documents)
│   ├── DocFlow.Worker/             # Background event handlers (MassTransit consumers)
│   ├── DocFlow.DocumentService/    # Phase 4: extracted Documents microservice
│   ├── DocFlow.Application/        # CQRS commands/queries, use cases
│   ├── DocFlow.Domain/             # Entities, domain events
│   ├── DocFlow.Infrastructure/     # EF Core, Blob, Service Bus, Key Vault
│   └── DocFlow.Contracts/          # Integration event definitions
├── tests/
│   ├── DocFlow.UnitTests/
│   └── DocFlow.IntegrationTests/   # Testcontainers / Aspire testing
└── .github/workflows/              # CI/CD
```

## Implementation Roadmap (~6–8 weeks)

**Phase 0 (Week 1): Azure setup**
- Create personal Azure free account, budget alert, resource group naming/tagging convention
- Install Azure CLI, `azd`, .NET 10 SDK, Docker
- Deploy a "hello world" Aspire app with `azd up`, then `azd down`

**Phase 1 (Weeks 1–2): Foundation**
- Solution structure, Clean Architecture, Aspire AppHost + ServiceDefaults
- PostgreSQL schema, EF Core models and migrations
- YARP gateway
- Deploy to Azure Container Apps + Azure PostgreSQL via `azd` and Bicep

**Phase 2 (Weeks 2–4): Core Features**
- Organization, User, Document APIs (CQRS)
- Document upload/download via Azure Blob Storage (SAS tokens)
- Tenant isolation (query filters, tenant from claims)
- Authentication with Microsoft Entra ID
- Managed Identity + Key Vault (no connection strings in config)

**Phase 3 (Weeks 4–5): Event-Driven & Real-Time**
- MassTransit + Azure Service Bus (topics, subscriptions, dead-letter queue)
- Document events (Created, Updated, Deleted) handled by Worker
- Audit log built from events (read model)
- SignalR (Azure SignalR Service) real-time dashboard

**Phase 4 (Weeks 5–6): Extract Documents Microservice**
- Move Documents module into `DocFlow.DocumentService` (separate Container App)
- Database-per-service: separate PostgreSQL database for documents
- Replace in-process calls with Service Bus events / HTTP via gateway
- Data consistency between services (outbox pattern via MassTransit)
- Distributed tracing across services in Application Insights
- ADR documenting the extraction and trade-offs

**Phase 5 (Weeks 6–7): Observability & DevOps**
- OpenTelemetry → Application Insights; KQL queries; alerts
- GitHub Actions CI/CD with OIDC federated credentials
- Cost review in Azure Cost Management

**Phase 6 (Weeks 7–8): Polish**
- Integration tests for multi-tenant scenarios
- README with architecture diagram, ADRs, setup + deploy guide
- Performance and security review (tenant isolation)

## Key Learning Goals

- [ ] Provision and deploy Azure resources with Bicep and `azd`
- [ ] Run containerized .NET apps on Azure Container Apps
- [ ] Use Managed Identity and Key Vault instead of secrets in config
- [ ] Authenticate users with Microsoft Entra ID
- [ ] Store and serve files with Blob Storage
- [ ] Event-driven messaging with Azure Service Bus
- [ ] Monitor with Application Insights and KQL
- [ ] CI/CD with GitHub Actions to Azure
- [ ] Extract a microservice from a modular monolith (database-per-service, outbox, distributed tracing)
- [ ] Multi-tenancy isolation strategies
- [ ] Clean Architecture + CQRS in C#
- [ ] Control and understand Azure costs

## Reference Documents

- **`/home/claude/docflow-project-spec.md`** — Original project specification (not in this repo; consider copying into `docs/`)
- **`/home/claude/azure-learning-plan.md`** — AZ-900 study plan (not in this repo; consider copying into `docs/`)
- **GitHub:** `docflow` (public repository) — Primary codebase

## How to Help with This Project

- Explain each Azure service before using it: what it is, why it's used, cost, AZ-900 relevance, and its .NET equivalent concept
- Prefer real Azure services; keep local emulators only for development speed
- Always mention cost impact of new Azure resources and the cheapest suitable tier
- Generate scaffolding, Bicep templates, EF Core models, CQRS handlers, MassTransit consumers
- Review for security issues (especially tenant isolation and secret handling)
- Write ADRs for significant decisions

## Communication Style

- Direct and pragmatic feedback preferred
- Honest trade-off analysis over hedged answers
- Connect new concepts to existing .NET/C# knowledge
- Simple, clear English
- Keep responses concise unless depth is specifically requested

## Status

- **Created:** 2026-10-09
- **Current Phase:** Planning
- **Next Step:** Phase 0 — Azure account + budget alert, tooling install, create GitHub repo and initial README
- **Parallel:** AZ-900 study (Microsoft Learn modules, practice exams)

---

*This document guides Claude's work on DocFlow. It is not a rigid spec but a north star for decision-making. Update it as the project evolves.*
