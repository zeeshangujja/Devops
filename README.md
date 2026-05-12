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


CI/CD Pipeline — GitHub Actions

Built a demo CI/CD pipeline using GitHub Actions
 for automated testing, Docker image building, and deployment.

Workflow
Code pushes on staging and main branches automatically trigger the pipeline
Runs lint checks and unit tests for backend and frontend
Builds Docker images after successful testing
Deploys staging and production environments separately
Uses SSH and Docker Compose for automated server deployment
Features Implemented
Branch-based deployment (staging and production separated)
Automated Docker image build and push
GitHub Secrets for secure credentials management
Commit SHA tagging for version tracking
Docker cache optimization for faster builds
Zero-downtime container restart using Docker Compose
Automatic Docker cleanup to save server disk space
Tools & Technologies
GitHub Actions
Docker & Docker Compose
Linux Server
SSH
CI/CD Automation
Git & GitHub

This was a hands-on demo project to practice DevOps workflow automation and deployment processes.

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
