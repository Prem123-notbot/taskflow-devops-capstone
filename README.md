# TaskFlow — DevOps Capstone

TaskFlow is the **actual application being tested and deployed** in this DevOps project: a project and task manager with user accounts, private projects, tasks, priorities, due dates, filters, dashboard progress, and health/metrics endpoints.

## Stack
Node.js, Express, SQLite, HTML/CSS/JavaScript, Docker, Docker Compose, Jenkins, Prometheus-compatible metrics.

## Run on Windows
Use Node.js 20+ and PowerShell:
```powershell
npm install
Copy-Item .env.example .env
npm start
```
Open http://localhost:3000, choose **Create account**, then create projects and tasks. The database is created under `data/`.

## Docker
```powershell
docker compose up --build -d
docker compose ps
docker compose logs -f taskflow
```
Open http://localhost:3000. Stop with `docker compose down`. Data persists in a Docker volume. `docker compose down -v` deletes that volume and its database.

## API checks
```powershell
Invoke-RestMethod http://localhost:3000/api/health
Invoke-RestMethod http://localhost:3000/api/ready
Invoke-RestMethod http://localhost:3000/api/info
Invoke-WebRequest http://localhost:3000/api/metrics
```

## GitHub
Create an empty GitHub repo, then:
```powershell
git init
git add .
git commit -m "Build TaskFlow project management app"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
git push -u origin main
```
Use your actual repository URL.

## Jenkins
The included Jenkinsfile assumes a Windows Jenkins agent with Git, Node/npm and Docker CLI available. Configure a Pipeline job from SCM. Stages: checkout, `npm ci`, `npm test`, Docker build. Configure Docker daemon access for the Jenkins agent before running the pipeline. For Linux agents, replace `bat` steps with `sh`.

## Main features
- Register, login and logout; password hashes are stored with bcrypt.
- Each account can access only its own projects and their tasks.
- Create projects and tasks; update task status/priority; edit/delete tasks.
- Search and filter tasks.
- Dashboard totals and project completion progress.
- `/api/health`, `/api/ready`, `/api/info`, `/api/metrics`.

## Security and limitations
Set a strong unique `SESSION_SECRET` before sharing/deploying. Use HTTPS in production. This is a learning project; add production-grade session storage, rate limiting, CSRF protection, backups and deployment hardening before real-world use. Never commit `.env` or database files.

## Demo flow
Register → create project → add tasks → change statuses → show dashboard progress → check health endpoint → run Jenkins tests/build → run container.

Webhook test: 2026-10-23 16:55:25
