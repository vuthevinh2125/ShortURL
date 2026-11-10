# ShortURL · URL shortener microservice

A URL shortener built as one service in a small .NET microservice system: an ASP.NET Core Web API behind an Ocelot API gateway, packaged with Docker Compose and built and tested on every push by GitHub Actions.

Group project at the University of Greenwich. My part is the **ShortenUrl service**; this repo also holds the gateway routing, the Docker Compose setup and the CI pipeline. The user/auth service is a teammate's, pulled in as a Docker image.

## Architecture

```
client ──► API Gateway (Ocelot, :5000)
              ├── /gateway/shorten/*  ──► ShortenUrl API (ASP.NET Core) ──► database
              └── /gateway/auth/*     ──► User service (teammate)
```

| Part | What it does |
|---|---|
| `ShortenUrl/` | Web API: create a short link, redirect by code, count hits. Repository pattern over EF Core (SQL Server), Swagger UI in development. |
| `Gateway/` | Ocelot gateway that routes `/gateway/shorten/*` and `/gateway/auth/*` to the right service. |
| `ShortenUrl.Tests/` | xUnit tests for the short-code generator. |
| `docker-compose.yml` | Database, ShortenUrl API, user service and gateway in one command. |
| `.github/workflows/dotnet.yml` | CI: restore, build and run the tests on every push and pull request to `main`. |

## API

| Method | Route | Result |
|---|---|---|
| `POST` | `/api/shortener/shorten` | Body `{ "originalUrl": "https://…" }`. Validates the URL (http/https only), generates a unique 7-character code (retries on collision), returns the short link. |
| `GET` | `/code/{code}` | Redirects to the original URL and updates the hit count and last-accessed time. |

## Run it

```bash
# whole system with Docker
docker compose up --build
# gateway on http://localhost:5000, ShortenUrl API on http://localhost:8081

# or just the API and tests with the .NET 8 SDK
dotnet test TinyUrlWebApp.slnx
dotnet run --project ShortenUrl
```

## Stack

C# · .NET 8 · ASP.NET Core Web API · Entity Framework Core · SQL Server · Ocelot · xUnit · Docker Compose · GitHub Actions
