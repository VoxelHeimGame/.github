# MMORPG Project: VoxelHeim

## Project Overview

VoxelHeim is an ambitious, open-source MMORPG project designed for web browsers. Our goal is to create a highly scalable, performant, and engaging multiplayer experience using modern web technologies and self-hosted infrastructure.

## Core Components and Projects

1. Client: "voxel-client"

1. Technologies: SolidJS, Three.js, TypeScript



2. Game Services:

1. "voxel-auth": Authentication service
2. "voxel-world": World management service
3. "voxel-player": Player data service
4. "voxel-combat": Combat system service
5. "voxel-inventory": Inventory management service
6. "voxel-chat": Chat system service



3. Support Services:

1. "voxel-analytics": Game analytics service
2. "voxel-admin": Game administration panel
3. "voxel-assets": Asset management service



4. Infrastructure Services:

1. "voxel-infra-admin": Infrastructure administration panel
2. "voxel-monitor": Monitoring and alerting service



5. Databases:

1. Auth DB: PostgreSQL + Citus
2. Game DB: TiDB (replacing CockroachDB)
3. Analytics DB: ClickHouse



6. Infrastructure:

1. Kubernetes for orchestration
2. NGINX for load balancing
3. Traefik as API Gateway (replacing Kong)
4. DragonFly DB for caching (replacing Redis)
5. RabbitMQ for messaging
6. MinIO for object storage
7. Prometheus, Grafana, and ELK Stack for monitoring and logging
8. Terraform for infrastructure as code





## Architecture Decisions

1. API Gateway: We've chosen Traefik as our API Gateway. It's open-source, feature-rich, and integrates well with Kubernetes.
2. Caching: DragonFly DB replaces Redis for its improved performance and compatibility with Redis clients.
3. Game DB: TiDB replaces CockroachDB. TiDB is fully open-source, MySQL compatible, and provides horizontal scalability.
4. Auth DB: PostgreSQL with Citus extension provides a robust, scalable solution for user authentication data.
5. Analytics DB: ClickHouse is retained for its exceptional performance in analytical queries on large datasets.


## Admin Panels

1. Game Admin Panel (voxel-admin):

1. User management
2. In-game economy controls
3. Content moderation
4. Event management



2. Infrastructure Admin Panel (voxel-infra-admin):

1. Service deployment and scaling
2. Database management
3. Performance monitoring
4. Log analysis





## Inter-Service Communication

Services communicate through:

1. RESTful API calls via Traefik for synchronous operations.
2. RabbitMQ for asynchronous event-driven communication.


## Game Interaction Examples

1. Player Combat:
The client sends a request to voxel-combat via Traefik. voxel-combat processes the attack, updates player states in voxel-player, and broadcasts results via WebSocket connections in voxel-world.
2. Inventory Management:
The client sends a request to voxel-inventory. voxel-inventory updates the TiDB database and publishes an event to RabbitMQ. voxel-player subscribes to these events and updates its local cache.


## Deployment and Scaling

1. All services are containerized and deployed on a Kubernetes cluster.
2. Horizontal Pod Autoscaler (HPA) automatically scales services based on CPU/memory usage or custom metrics.
3. TiDB, PostgreSQL (with Citus), and ClickHouse handle database scaling through their distributed architectures.


## Development Workflow

1. Developers work on feature branches in their local environments.
2. Pull requests are created for code review.
3. GitHub Actions run automated tests and builds.
4. After approval and successful tests, code is merged to the main branch.
5. GitHub Actions deploy to a staging environment for final testing.
6. Production deployment is triggered manually through the voxel-infra-admin panel.


## Getting Started

1. Clone the repository
2. Install dependencies (Bun is our package manager of choice)
3. Set up local development databases
4. Run the development server
5. Access the game client at localhost:3000


## Contributing

We welcome contributions! Please read our CONTRIBUTING.md file for guidelines on how to submit issues, feature requests, and pull requests.

## License

This project is licensed under the MIT License - see the LICENSE.md file for details.

Remember, VoxelHeim is an ambitious project that will evolve over time. Regular architecture reviews and performance optimizations will be crucial as we scale. Happy coding, and let's build an amazing MMORPG together!
