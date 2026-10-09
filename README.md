# DocFlow

Multi-tenant document management SaaS built with **.NET 10** and **Microsoft Azure**.

A learning and portfolio project focused on real, hands-on cloud architecture: deploying, securing, and monitoring a .NET system on Azure — starting as a modular monolith and evolving into microservices.

> 🚧 **Status:** Phase 0 — project setup. Nothing to run yet.

## Goals

- Build and deploy a real cloud application on Azure
- Use a modern .NET stack: .NET Aspire, minimal APIs, EF Core, OpenTelemetry
- Practice cloud architecture patterns: multi-tenancy, CQRS, event-driven messaging, API gateway, microservice extraction
- Infrastructure as Code (Bicep) and CI/CD (GitHub Actions)
- Keep cloud costs under $10/month

## Tech Stack

| Area | Technology | Azure Service |
|------|-----------|---------------|
| Runtime | .NET 10, ASP.NET Core | Azure Container Apps |
| Orchestration | .NET Aspire, Azure Developer CLI (`azd`) | — |
| API Gateway | YARP | Azure Container Apps |
| Database | PostgreSQL, EF Core | Azure Database for PostgreSQL |
| File storage | Azure Blob Storage SDK | Azure Blob Storage |
| Messaging | MassTransit | Azure Service Bus |
| Real-time | SignalR | Azure SignalR Service |
| Identity | OpenID Connect / JWT | Microsoft Entra ID |
| Secrets | Managed Identity | Azure Key Vault |
| Observability | OpenTelemetry, Serilog | Application Insights |
| IaC / CI/CD | Bicep, GitHub Actions | Azure Resource Manager |

## Architecture

```
            ┌──────────────┐
 Client ───▶│ YARP Gateway │
            └──────┬───────┘
                   │
       ┌───────────┴────────────┐
       ▼                        ▼
 ┌───────────┐          ┌─────────────────┐
 │ DocFlow   │          │ Document        │   (extracted in Phase 4)
 │ API       │          │ Service         │
 │ (modules) │          └────────┬────────┘
 └─────┬─────┘                   │
       │      Azure Service Bus  │
       └──────────┬──────────────┘
                  ▼
           ┌─────────────┐
           │   Worker    │
           └─────────────┘
```

Detailed design: [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) (coming soon).

## Roadmap

- [ ] **Phase 0** — Azure account, tooling, repository setup
- [ ] **Phase 1** — Solution structure, Aspire, PostgreSQL, first Azure deployment
- [ ] **Phase 2** — Core APIs, Blob Storage, tenant isolation, Entra ID, Key Vault
- [ ] **Phase 3** — Service Bus events, audit log, SignalR dashboard
- [ ] **Phase 4** — Extract Documents into a microservice
- [ ] **Phase 5** — Observability, CI/CD
- [ ] **Phase 6** — Tests, documentation, polish

## Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (or Podman)
- [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli)
- [Azure Developer CLI (`azd`)](https://learn.microsoft.com/azure/developer/azure-developer-cli/install-azd)
- An Azure subscription (free account works)

## Getting Started

Instructions will be added in Phase 1.
