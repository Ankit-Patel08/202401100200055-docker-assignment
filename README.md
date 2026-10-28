# Docker & Container Security Assessment
**Registration Number:** 202401100200055  
**Project:** FestPass Registration Application  
**Progress Branch:** `assessment-progress`
## Overview
FestPass is a containerized event registration application built for the Docker & Container Security practical assessment. It uses a Flask backend, an Nginx frontend, and PostgreSQL for persistent registration data.
## Architecture
- **Frontend:** Nginx serving the registration interface and proxying API requests.
- **Backend:** Flask REST API running in Docker.
- **Database:** PostgreSQL with a named volume for persistent data.
- **Networking:** Separate frontend and backend networks; the backend connects to PostgreSQL through the backend network.
- **Configuration:** Environment variables are supplied through a local `.env` file.
## Project Structure
```text
.
├── backend/
│   ├── Dockerfile
│   ├── app.py
│   ├── requirements.txt
│   └── .dockerignore
├── frontend/
│   ├── index.html
│   └── nginx.conf
├── reports/
│   ├── trivy-before.txt
│   └── trivy-after.txt
├── screenshots/
├── docker-compose.yml
├── .env.example
└── .gitignore
```
## Run Locally
1. Create a local environment file:
   ```powershell
   Copy-Item .env.example .env
   ```
2. Review `.env` and set appropriate local values. The default host port in this development setup is `8135`, because Windows reserves port `135` for RPC.
3. Start the services:
   ```powershell
   docker compose up -d --build
   ```
4. Open the frontend at `http://localhost:8135`.
5. Check backend health:
   ```powershell
   curl.exe http://localhost:8135/api/health
   ```
6. Stop the services:
   ```powershell
   docker compose down
   ```
The PostgreSQL named volume preserves registrations across container restarts. To remove the stored database data as well, use `docker compose down -v` only when you intentionally want to delete it.
## Container Security
The hardened backend image (`fest-backend:0.2`) uses a slim Python base image, updated Python dependencies, and a non-root application user.
Validation performed:
- Backend health endpoint returned the expected registration number.
- Container execution was verified as `appuser` (UID 1000).
- Trivy scan reports are stored in `reports/`.
- Network isolation and PostgreSQL persistence were tested.
The Trivy report should be reviewed for both fixed and unfixed vulnerabilities. The critical-only check used `--ignore-unfixed`; it does not imply that the full image scan has zero critical findings.
## Evidence
Assessment screenshots are stored in `screenshots/`. Trivy scan outputs are stored in `reports/`.
## Current Status
- [x] Backend image build and standalone health check
- [x] Docker Compose frontend, backend, and PostgreSQL setup
- [x] Network isolation and database persistence checks
- [x] Trivy baseline and hardened-image scans
- [x] Non-root container execution check
- [ ] Docker Hub release and remote-image verification
- [ ] Cosign signing and verification
## Security Notes
- Do not commit `.env`, passwords, access tokens, or private signing keys.
- `.env.example` contains placeholders for local configuration.
- Only the public Cosign key should be committed if the signing stage requires it.
