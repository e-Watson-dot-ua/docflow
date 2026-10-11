# DocFlow

DocFlow is a multi-tenant document management SaaS application. It uses **.NET 10** and **Microsoft Azure**.

DocFlow is a learning and portfolio project. It shows real cloud architecture on Azure: the deployment, security, and monitoring of a .NET system. The system starts as a modular monolith. Later, one module becomes a separate microservice.

> 🚧 **Status:** Phase 0 — project setup. You cannot run the application yet.

## Goals

- Build a real cloud application and deploy it to Azure.
- Use a modern .NET stack: .NET Aspire, minimal APIs, EF Core, and OpenTelemetry.
- Use cloud architecture patterns: multi-tenancy, CQRS, event-driven messages, an API gateway, and the extraction of a microservice.
- Use Infrastructure as Code (Bicep) and CI/CD (GitHub Actions).
- Keep the Azure cost below $10 each month.

## Tech Stack

| Area | Technology | Azure Service |
|------|-----------|---------------|
| Runtime | .NET 10, ASP.NET Core | Azure Container Apps |
| Orchestration | .NET Aspire, Azure Developer CLI (`azd`) | — |
| API gateway | YARP | Azure Container Apps |
| Database | PostgreSQL, EF Core | Azure Database for PostgreSQL |
| File storage | Azure Blob Storage SDK | Azure Blob Storage |
| Messages | MassTransit | Azure Service Bus |
| Real-time updates | SignalR | Azure SignalR Service |
| Identity | OpenID Connect / JWT | Microsoft Entra ID |
| Secrets | Managed Identity | Azure Key Vault |
| Observability | OpenTelemetry, Serilog | Application Insights |
| IaC and CI/CD | Bicep, GitHub Actions | Azure Resource Manager |

## Architecture

```
            ┌──────────────┐
 Client ───▶│ YARP Gateway │
            └──────┬───────┘
                   │
       ┌───────────┴────────────┐
       ▼                        ▼
 ┌───────────┐          ┌─────────────────┐
 │ DocFlow   │          │ Document        │   (separate service from Phase 4)
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

The YARP gateway sends all client requests to the correct service. The API and the Document Service send events through Azure Service Bus. The Worker receives these events and processes them.

For the full design, see [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md). This document is not available yet.

## Roadmap

- [ ] **Phase 0** — Azure account, tools, and repository setup
- [ ] **Phase 1** — Solution structure, Aspire, PostgreSQL, and the first deployment to Azure
- [ ] **Phase 2** — Core APIs, Blob Storage, tenant isolation, Entra ID, and Key Vault
- [ ] **Phase 3** — Service Bus events, audit log, and SignalR dashboard
- [ ] **Phase 4** — Move the Documents module into a separate microservice
- [ ] **Phase 5** — Observability and CI/CD
- [ ] **Phase 6** — Tests, documentation, and final improvements

## Requirements

Install these tools:

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) or Podman
- [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli)
- [Azure Developer CLI (`azd`)](https://learn.microsoft.com/azure/developer/azure-developer-cli/install-azd)

You also need an Azure subscription. A free account is sufficient.

## How to Start

Phase 1 will add the instructions to build and run DocFlow.
