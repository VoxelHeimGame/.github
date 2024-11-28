# 🌍 **VoxelHeim MMORPG Project** 🎮

## 📋 Project Overview

**VoxelHeim** is an ambitious, open-source MMORPG project designed for web browsers. Our goal is to create a highly scalable, performant, and engaging multiplayer experience using modern web technologies and self-hosted infrastructure. This document provides a comprehensive overview of our architecture, technology choices, and development practices.

---

## 📂 GitHub Organization Structure

Our project is organized into the following repositories within the **VoxelHeim** GitHub organization:

- **voxelheim-client**
- **voxelheim-auth-service**
- **voxelheim-world-service**
- **voxelheim-player-service**
- **voxelheim-combat-service**
- **voxelheim-inventory-service**
- **voxelheim-chat-service**
- **voxelheim-analytics-service**
- **voxelheim-admin-panel**
- **voxelheim-assets-service**
- **voxelheim-infra-admin**
- **voxelheim-monitoring**
- **voxelheim-infrastructure**
  - **kubernetes-configs**
  - **terraform-modules**
  - **docker-compose**
- **voxelheim-docs**

Each repository has its own project board for task management, accessible at: [Project Boards](https://github.com/VoxelHeim/<repo-name>/projects).

---

## ⚙️ Core Components and Projects

### 1. **Client: "voxelheim-client"** 🖥️

- **Technologies:** SolidJS, Three.js, TypeScript
- **Purpose:** Renders the game world, handles user input, and communicates with the server.

  **Key Features:**
  - 3D rendering using Three.js 🌐
  - State management with SolidJS 🔄
  - WebSocket communication for real-time updates 🔌

  [Project Board](https://github.com/VoxelHeim/voxelheim-client/projects)

---

### 2. **Game Services** ⚔️

All game services are built using **Node.js** and **TypeScript**, designed to be stateless for horizontal scalability.

- **voxelheim-auth-service:** Authentication service 🔐
- **voxelheim-world-service:** World management service 🌎
- **voxelheim-player-service:** Player data service 👾
- **voxelheim-combat-service:** Combat system service ⚔️
- **voxelheim-inventory-service:** Inventory management service 🎒
- **voxelheim-chat-service:** Chat system service 💬

Each service has its own project board at: [Service Project Boards](https://github.com/VoxelHeim/<service-name>/projects)

---

### 3. **Support Services** 🛠️

- **voxelheim-analytics-service:** Game analytics service 📊
- **voxelheim-admin-panel:** Game administration panel 🛡️
- **voxelheim-assets-service:** Asset management service 🗃️

---

### 4. **Infrastructure Services** 🏗️

- **voxelheim-infra-admin:** Infrastructure administration panel 🖥️
- **voxelheim-monitoring:** Monitoring and alerting service 🚨

---

### 5. **Databases** 🗃️

- **Game DB:** PostgreSQL + Citus (for game state, player data, inventory, etc.)
  - Scaled horizontally for high write throughput 💥

- **Auth DB:** PostgreSQL + Citus (separate cluster for authentication data)
  - Optimized for read-heavy operations 📚

- **Analytics DB:** ClickHouse (for analytical queries on large datasets)
  - Optimized for analyzing game events, player behavior, and system performance 📈

---

Documentation Links:
- [PostgreSQL Docs](https://www.postgresql.org/docs/)
- [Citus Docs](https://docs.citusdata.com/)
- [ClickHouse Docs](https://clickhouse.com/docs/)

---

### 6. **Infrastructure** 🏠

- **Kubernetes:** Orchestrates all services and databases 🚢
- **NGINX:** Acts as a reverse proxy and load balancer 🔄
- **Traefik:** API Gateway for routing requests 🔀
- **DragonFly DB:** In-memory caching for frequently accessed data ⚡
- **RabbitMQ:** Messaging between services 📬
- **MinIO:** Stores large binary objects (game assets) 📦
- **Prometheus, Grafana, and ELK Stack:** Monitoring, alerting, and log management 📊
- **Terraform:** Infrastructure as code management 🛠️

Documentation Links:
- [Kubernetes Docs](https://kubernetes.io/docs/)
- [NGINX Docs](https://nginx.org/en/docs/)
- [Traefik Docs](https://doc.traefik.io/traefik/)
- [DragonFly DB Docs](https://dragonflydb.io/docs)
- [RabbitMQ Docs](https://www.rabbitmq.com/documentation.html)
- [MinIO Docs](https://min.io/docs/minio/linux/index.html)
- [Prometheus Docs](https://prometheus.io/docs/)
- [Grafana Docs](https://grafana.com/docs/)
- [ELK Stack Docs](https://www.elastic.co/guide/index.html)
- [Terraform Docs](https://developer.hashicorp.com/terraform/docs)

---

## 🛠️ Architecture Decisions and Explanations

### Asset Management (voxelheim-assets-service)

- Critical assets are bundled directly into the client for immediate loading.
- Secondary assets are loaded through the assets service as needed.
- Implements lazy loading and streaming techniques for 3D assets.
- Uses a CDN for global asset distribution.

This approach balances initial load time with dynamic content delivery, ensuring a smooth user experience while managing bandwidth efficiently.

### Databases

We use PostgreSQL with Citus extension for both the game and authentication databases, but in separate clusters:

1. **Game DB (PostgreSQL + Citus):**
   - Handles game state, player data, inventory, etc.
   - Scaled horizontally for high write throughput.
   - Citus allows for data sharding across multiple nodes, enabling high performance for a large number of concurrent players.

2. **Auth DB (PostgreSQL + Citus):**
   - Manages authentication and user account data.
   - Scaled for read-heavy operations.
   - Separate from the game DB to isolate sensitive user data and optimize authentication workflows.

3. **Analytics DB (ClickHouse):**
   - Optimized for analytical queries on large datasets.
   - Enables real-time analysis of game events, player behavior, and system performance.

### Caching with DragonFly DB

DragonFly DB serves as our distributed caching layer:

- Provides in-memory caching for frequently accessed data, reducing load on main databases.
- Improves response times for common queries, enhancing overall game performance.
- Acts as a distributed cache, supporting our microservices architecture.
- Offers Redis compatibility, allowing easy integration with existing systems and libraries.

Use cases:
1. Caching player session data for quick access across services.
2. Storing temporary game state (e.g., player positions) for fast retrieval.
3. Caching frequently accessed game items or NPCs.

### Messaging System: RabbitMQ

We chose **RabbitMQ** for its:

- Low latency for individual messages, crucial for real-time game events.
- Better support for complex messaging patterns, allowing flexible communication between services.
- Ease of setup and maintenance, reducing operational overhead.

---

## 🏗️ Infrastructure Setup

### Kubernetes

- Located in `voxelheim-infrastructure/kubernetes-configs/`
- Includes deployments, services, and ingress configurations for all microservices.
- Enables easy scaling and management of our containerized services.
- Provides automatic load balancing and service discovery.
- Allows for rolling updates and easy rollbacks of services.

Kubernetes is central to our infrastructure, providing:
1. Orchestration of all our microservices and databases.
2. Automatic scaling based on load.
3. Self-healing capabilities (restarting failed containers).
4. Efficient resource utilization across our cluster.

### Terraform

- Located in `voxelheim-infrastructure/terraform-modules/`
- Defines and manages cloud resources (e.g., VMs, networks, load balancers).
- Ensures infrastructure consistency across environments.

### Docker

- `voxelheim-infrastructure/docker-compose/docker-compose.yml` for local development environment.
- Allows developers to run the entire stack locally for testing and development.

### Database Clusters

- Managed through Kubernetes StatefulSets.
- Configurations in `voxelheim-infrastructure/kubernetes-configs/databases/`.
- Ensures data persistence and proper scaling of database clusters.

### Load Balancers

- **NGINX Ingress Controller** for Kubernetes-level load balancing.
- Cloud-native load balancers (defined in Terraform) for distributing traffic to Kubernetes nodes.
- Ensures efficient distribution of incoming requests across our services.

---

## 🔄 Communication Flow and Examples

### Example 1: Player Combat

When a player attacks another player:
1. The client sends an attack request to the API Gateway (Traefik).
2. Traefik routes the request to the Combat Service.
3. The Combat Service:
   - Retrieves attacker and defender data from the Player Service.
   - Calculates damage using combat algorithms.
   - Updates player health in the Player Service.
4. Sends combat result to the World Service for broadcasting.
5. World Service broadcasts the combat result to relevant clients via WebSocket.
6. The client updates the UI to reflect the combat outcome.

Data flow:
- Player positions and basic info cached in DragonFly DB for quick access.
- Combat results stored in Game DB (PostgreSQL + Citus).
- Combat events logged to ClickHouse for analytics.

---

## 📈 Monitoring & Logging

- **Prometheus** collects metrics on service performance and health.
- **Grafana** visualizes these metrics for real-time monitoring.
- **ELK Stack** (Elasticsearch, Logstash, Kibana) for log aggregation, processing, and visualization.
  - Logs all game events, errors, and user activity.
  - Provides a centralized place to search, filter, and analyze logs.
  
---

## 📝 Contributing

We welcome contributions from developers! Please refer to our [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to get involved.

---

## 📢 License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for more details.
