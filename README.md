# Local development

The application is split across separate GitHub repositories. Clone the repositories into sibling folders with these names:

```powershell
New-Item -ItemType Directory -Path Campus-DigiYak
Set-Location Campus-DigiYak
git clone https://github.com/Campus-DigiYak/infra.git
git clone https://github.com/Campus-DigiYak/backend.git
git clone https://github.com/Campus-DigiYak/frontend.git
git clone https://github.com/Campus-DigiYak/docs.git
```

The `.github` organization-profile repository is not required to run the application.

## Start PostgreSQL/PostGIS and Redis

Prerequisite: Docker Desktop must be running.

From the shared `Campus-DigiYak` parent directory, create the local Compose environment file and start the services:

```powershell
Copy-Item infra\.env.example infra\.env
docker compose --env-file infra\.env -f infra\docker-compose.yml up -d
docker compose --env-file infra\.env -f infra\docker-compose.yml ps
```

The Compose file starts PostgreSQL 18 with PostGIS and Redis 8. For the PostgreSQL 18 image layout, its named data volume is mounted at `/var/lib/postgresql`. Its health checks should report healthy before starting the API. The local `.env` file is ignored by Git; do not commit real credentials.

The same commands on macOS/Linux are:

```sh
cp infra/.env.example infra/.env
docker compose --env-file infra/.env -f infra/docker-compose.yml up -d
docker compose --env-file infra/.env -f infra/docker-compose.yml ps
```

## Run the backend

Open a second terminal in the shared parent directory.

Windows PowerShell:

```powershell
Set-Location backend
.\gradlew.bat bootRun
```

> **Note:** Gradle will show `> :bootRun` stuck at roughly 80% — this is normal. The web server runs indefinitely inside that Gradle task, so the progress bar never reaches 100%. Once you see `Started DigiyakApplication in …` in the log output, the API is ready. Press `Ctrl+C` to stop it.

macOS/Linux:

```sh
cd backend
./gradlew bootRun
```

The Gradle wrapper downloads the repository-pinned Gradle version; a global Gradle installation is not needed. Local defaults connect to PostgreSQL at `localhost:5432` and Redis at `localhost:6379`.

Backend tests:

```powershell
Set-Location backend
.\gradlew.bat test
```

```sh
cd backend
./gradlew test
```

## Run the frontend

Open a third terminal in the shared parent directory:

```powershell
Set-Location frontend
pnpm install
pnpm dev
```

The development server is available at <http://localhost:3000>. The API is available at <http://localhost:8080>.

## Stop local services

From the shared parent directory:

```powershell
docker compose --env-file infra\.env -f infra\docker-compose.yml down
```

This keeps the database volumes. To remove the database and Redis data as well, run `docker compose --env-file infra\.env -f infra\docker-compose.yml down -v`.

## Repository layout

Each folder is an independent Git repository. Clone or pull each repository separately; cloning one does not fetch its siblings. Keep the folder names shown above so the commands and Compose paths resolve correctly.
