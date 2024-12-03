# 🌍 VoxelHeim MMORPG Project 🎮

## 📋 Project Overview

VoxelHeim is an ambitious, open-source MMORPG project designed for web browsers. Our goal is to create a highly scalable, performant, and engaging multiplayer experience using modern web technologies and self-hosted infrastructure.

## 🏗️ Infrastructure and Environment Management

### Environments

We maintain several environments to ensure smooth development, testing, and deployment:

1. **DEV**: Personal development environment for developers
2. **TEST**: Internal testing environment for developers and QA
3. **PTU (Public Test Universe)**: Public testing environment
4. **LIVE**: Production environment

### Development Workflow

1. Developers work in their personal **DEV** environment
2. Changes are pushed to the **TEST** environment for internal testing
3. Approved changes move to **PTU** for public testing
4. Finally, tested and approved changes are deployed to the **LIVE** environment

### ArgoCD for Deployment Management

We use ArgoCD for managing deployments across our environments:

- **Repository**: `voxelheim-infrastructure/argocd-configs/`
- **Purpose**: Automate deployments and ensure consistency across environments
- **Features**:
  - GitOps workflow
  - Automatic synchronization of Kubernetes manifests
  - Rollback capabilities
  - Multi-cluster support

#### ArgoCD Setup

1. Install ArgoCD in your Kubernetes cluster
2. Configure ArgoCD to watch your infrastructure repository
3. Define Application resources for each service and environment

Example ArgoCD Application for the auth service in the TEST environment:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: auth-service-test
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/VoxelHeim/voxelheim-infrastructure.git
    targetRevision: HEAD
    path: kubernetes-configs/test/auth-service
  destination:
    server: https://kubernetes.default.svc
    namespace: voxelheim-test
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### Developer Environment Setup

To set up a personal development environment:

1. Clone the necessary repositories
2. Use `docker-compose` for local development:
   ```bash
   cd voxelheim-infrastructure/docker-compose
   docker-compose up -d
   ```
3. For database management:
   - Use the provided scripts in `voxelheim-infrastructure/scripts/` to create and manage database dumps
   - Example: `./create_db_dump.sh dev` to create a dump of the DEV environment database

### Database Management

- Use Kubernetes CronJobs to schedule regular database backups
- Store database dumps in MinIO for easy access and management
- Provide scripts for developers to easily import database dumps into their local environment

Example script to import a database dump:

```bash
#!/bin/bash
# Import database dump
ENV=$1
DUMP_FILE=$2

if [ "$ENV" = "dev" ]; then
  docker-compose exec postgres psql -U postgres -d voxelheim < $DUMP_FILE
else
  kubectl exec -it $(kubectl get pods -l app=postgres -o jsonpath="{.items[0].metadata.name}" -n voxelheim-$ENV) -- psql -U postgres -d voxelheim < $DUMP_FILE
fi
```

## 📂 GitHub Organization Structure

Our project is organized into the following repositories within the VoxelHeim GitHub organization:

- voxelheim-client
- voxelheim-auth-service
- voxelheim-world-service
- voxelheim-player-service
- voxelheim-combat-service
- voxelheim-inventory-service
- voxelheim-chat-service
- voxelheim-analytics-service
- voxelheim-admin-panel
- voxelheim-assets-service
- voxelheim-infra-admin
- voxelheim-monitoring
- voxelheim-infrastructure
  - kubernetes-configs
  - terraform-modules
  - docker-compose
  - argocd-configs
- voxelheim-docs

## ⚙️ Core Components and Technologies

- **Frontend**: SolidJS, Three.js, TypeScript
- **Backend**: Node.js, TypeScript
- **Databases**: PostgreSQL + Citus, ClickHouse
- **Caching**: DragonFly DB
- **Message Queue**: RabbitMQ
- **Infrastructure**: Kubernetes, Docker, Terraform, ArgoCD
- **API Gateway**: Traefik
- **Monitoring**: Prometheus, Grafana, ELK Stack

## 🛠️ Architecture Decisions

### Databases

1. **Game DB (PostgreSQL + Citus)**: Handles game state, player data, inventory, etc.
2. **Auth DB (PostgreSQL + Citus)**: Manages authentication and user account data.
3. **Analytics DB (ClickHouse)**: Optimized for analytical queries on large datasets.

### Caching with DragonFly DB

Provides in-memory caching for frequently accessed data, reducing load on main databases.

### Messaging System: RabbitMQ

Chosen for its low latency and support for complex messaging patterns.

## 🏗️ Infrastructure Setup

- **Kubernetes**: Orchestrates all services and databases
- **Terraform**: Defines and manages cloud resources
- **Docker**: Used for local development environments
- **ArgoCD**: Manages deployments across environments

## 📈 Monitoring & Logging

- **Prometheus**: Collects metrics on service performance and health
- **Grafana**: Visualizes these metrics for real-time monitoring
- **ELK Stack**: Log aggregation, processing, and visualization

## 📝 Contributing

We welcome contributions from developers! Please refer to our [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to get involved.

## 📢 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.
