# About Docker

- **Why Docker exists**: Before containers, you shipped code that said "works on my machine." Docker packages your app + its environment (OS libs, runtime, configs) into a portable image. That's it. The "why" is reproducibility and isolation.

- **Why Kubernetes exists**: Once you have 50 containers running across 10 servers — who restarts a crashed one? Who routes traffic? Who handles deploys with zero downtime? Kubernetes is the answer. It's an orchestrator. Docker runs containers; Kubernetes manages fleets of them.

- **When companies choose what**:
  - Solo app or small team → Docker Compose (multiple containers, one machine)
  - Scale, multiple services, production reliability needed → Kubernetes
  - Serverless/managed → ECS (AWS), Cloud Run (GCP) — Kubernetes without the ops burden
