# Kubernetes Portfolio: Multi-Container Orchestration & CI Pipeline

An engineering blueprint demonstrating containerized application delivery and native Kubernetes orchestration using a branch-conditional CI pipeline.

🛠️ Architecture Highlights
- Orchestration: Manages high-availability deployments inside a local Minikube cluster with decoupled multi-container services.
- Frontend Layer: Runs a 2-replica Nginx deployment with explicit resource limits (128Mi RAM, 200m CPU hard caps) exposed via a `NodePort` Service.
- Stateful Backend: Runs a single-instance PostgreSQL 15 database container mapped to an isolated 1Gi PersistentVolumeClaim (`postgres-pvc`) for data durability.
- Configuration & Secrets: Decouples active application profiles into a native `ConfigMap` while abstracting database credentials within an opaque Kubernetes `Secret`.

---

📁 Repository Blueprint
```utils
k8-portfolio/
├── .github/workflows/
│   └── ci-cd.yml                   # Branch-conditional CI linting & image build pipeline
├── kubernetes/
│   ├── frontend-app.yaml           # Frontend Deployment (2 replicas) & NodePort Service
│   ├── frontend-configmap.yaml     # Decoupled environment profiles
│   ├── postgres-db.yaml            # Stateful Postgres Deployment & ClusterIP Service
│   ├── postgres-pvc.yaml           # Persistent 1Gi Storage Claim for database engine
│   └── secret.yaml                 # Opaque storage for database credentials
├── webpages/
│   ├── about.html                  # Multi-page website assets
│   ├── contact.html
│   ├── index.html                  # Core routing portal
│   └── purpose.html
└── Dockerfile                      # Builds the Nginx layer copying website content
```

---

🔄 Branch-Conditional CI Pipeline

The GitHub Actions workflow (`ci-cd.yml`) utilizes dual-gate execution paths to save runner minutes based on branch targeting:

- Development Flow (`dev-1` branch)
    - Fast Validation: Skips intensive build stages on development commits.
    - Workspace Linting: Validates workspace health by running automated checks to ensure all core Kubernetes manifests and Dockerfiles exist before pull request reviews.
- Production Delivery Flow (`main` branch)
    - Docker Hub Authentication: Securely logs in using automated repository secrets.
    - Buildx Compiling: Leverages `docker/setup-buildx-action` to handle advanced layer caching.
    - Dual-Tag Distribution: Automatically builds and pushes the image to Docker Hub, concurrently applying the `latest` tag and a unique Git commit SHA (`${{ github.sha }}`) track.

---

🔒 Required GitHub Secrets
To allow successful image publishing onto your registry, configure these credentials under Settings > Actions > Secrets:
- `DOCKERHUB_USERNAME` (Your registry namespace user)
- `DOCKERHUB_TOKEN` (Secure personal access token for registry pushing)
