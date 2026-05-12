🚀 DevOps Portfolio — CI/CD Pipeline Demo

Note: This is a sanitized demo repository. All company names, internal hostnames,
IP addresses, and project-specific details have been removed per NDA obligations.
The code reflects real patterns and practices used in production environments.


📌 Overview
This repository showcases a production-grade DevOps setup including:

Separate Dockerfiles for staging and production environments
Full-stack Angular frontend + Node.js backend containerization
Complete GitHub Actions CI/CD pipeline (test → build → push → deploy)


🗂️ Repository Structure
devops-portfolio-demo/
├── Dockerfile.backend.prod           # Backend production build
├── Dockerfile.backend.stage          # Backend staging build
├── Dockerfile.frontend.prod          # Frontend production build
├── Dockerfile.frontend.stage         # Frontend staging build
└── .github/
    └── workflows/
        └── ci-cd.yml                 # GitHub Actions CI/CD pipeline

🐳 Dockerfile — Backend
Files: Dockerfile.backend.prod / Dockerfile.backend.stage
What it does
StagePurposeInstall system depsInstalls qpdf for PDF processing in same layer to keep image smallInstall node depsCopies package.json first to use Docker layer cachingCopy sourceCopies backend source code after deps are installedStartupRuns the server via npm script
Key practices applied

Layer caching — package.json copied before source code so npm ci only reruns when dependencies change
apt cache cleared — rm -rf /var/lib/apt/lists/* runs in same layer to keep image size minimal
Production vs Staging — prod uses npm ci --only=production (no dev deps), staging uses npm ci (includes dev tools for debugging)
Environment variables — port and host injected via ${PORT} and ${HOST}, never hardcoded


🐳 Dockerfile — Frontend
Files: Dockerfile.frontend.prod / Dockerfile.frontend.stage
What it does
StagePurposeInstall serverInstalls angular-http-server globally for HTML5 routing supportInstall depsCopies package.json first to leverage layer cachingBuild appCompiles Angular app via npm build scriptStartupServes compiled dist folder via angular-http-server
Key practices applied

angular-http-server — used instead of plain http-server because it handles Angular HTML5 routing correctly
Production vs Staging — prod build is minified and tree-shaken, staging build has source maps enabled for easier debugging
Layer caching — dependencies installed before source copy so rebuilds are fast


⚙️ CI/CD Pipeline — GitHub Actions
File: .github/workflows/ci-cd.yml
Pipeline Flow
git push (main or staging branch)
        │
        ▼
  ┌─────────────┐
  │  1. Test    │  lint + unit tests — backend & frontend
  └──────┬──────┘
         │ pass
         ├─────────────────────────┐
         │                         │
   staging branch             main branch
         │                         │
  ┌──────▼──────┐           ┌──────▼──────┐
  │  2. Build   │           │  3. Build   │
  │  Staging    │           │  Production │
  │  (2 images) │           │  (2 images) │
  └──────┬──────┘           └──────┬──────┘
         │                         │
  ┌──────▼──────┐           ┌──────▼──────┐
  │  4. Deploy  │           │  5. Deploy  │
  │  Staging    │           │  Production │
  │  Server     │           │  Server     │
  └─────────────┘           └─────────────┘
Jobs Breakdown
JobTriggerWhat runstestevery pushlint + unit tests on backend and frontendbuild-stagingstaging branch onlybuilds 2 Docker images → pushes to registrybuild-productionmain branch onlybuilds 2 Docker images → pushes to registrydeploy-stagingafter build-stagingSSH into staging server → pull images → restart containersdeploy-productionafter build-productionSSH into production server → pull images → restart containers
Key practices applied

Job dependencies — build only runs if test passes, deploy only runs if build passes
Branch conditions — pushing to staging never touches production server and vice versa
GitHub Actions cache — Docker layers cached between runs via type=gha to speed up builds
SHA image tagging — every image tagged with commit SHA so every build is traceable to exact commit
Secrets management — server host and SSH keys stored in GitHub Secrets, never appear in code or logs
Zero-downtime deploy — docker-compose up -d restarts containers without dropping the service
Disk cleanup — docker system prune -f runs after every deploy to free server disk space


🔐 Security Highlights

No secrets or passwords hardcoded anywhere — all via GitHub Secrets and environment variables
Production image has zero dev dependencies installed
Port and host values injected at runtime via environment variables
SSH deployment uses a dedicated deploy user — not root
Staging environment fully separate from production — production is never used for testing
apt package cache cleared in same Docker layer to reduce attack surface


🛠️ Tech Stack
CategoryToolBackend RuntimeNode.js 20.11.1Frontend FrameworkAngularPDF ProcessingqpdfHTTP Serverangular-http-serverContainerizationDockerCI/CDGitHub ActionsImage RegistryGitHub Container Registry (GHCR)

📄 License
This repository is shared for portfolio viewing purposes only.
All code is generic and does not contain any proprietary business logic.
© 2025 — All rights reserved. Do not copy or reuse without permission.
