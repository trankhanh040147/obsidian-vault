# Nexus Pulse AI

AI-powered content creation platform for content creators, F&B businesses, and marketers. Generate stunning posters, menus, and marketing materials using Google's Gemini AI.

## Features

- **Poster/Ads Generation** - Create professional marketing posters and advertisements
- **Menu Creation** - Generate beautiful F&B menus with multiple layouts (coming soon)
- **Product Design** - Design product packaging and illustrations (coming soon)
- **Google OAuth2** - Secure authentication with Google accounts
- **Rate Limiting** - Fair usage limits per user

## Tech Stack

| Component | Technology |
|-----------|------------|
| Backend | Go 1.24+ with Gin framework |
| Database | PostgreSQL 15 |
| Cache | Redis 7 |
| AI Model | Google Gemini API |
| Auth | Google OAuth2 + JWT |
| Container | Docker & Docker Compose |

## Project Structure

```
.
├── cmd/server/          # Application entry point
├── config/              # Configuration management
├── docker/              # Docker configurations
├── internal/
│   ├── models/          # Domain entities
│   ├── must/            # Initialization helpers
│   ├── presentation/    # HTTP handlers & middleware
│   ├── repositories/    # Data access layer
│   ├── storage/         # File storage abstraction
│   └── usecase/         # Business logic
├── pkg/
│   ├── l/               # Legacy logger
│   ├── log/             # Structured logging
│   └── storage/         # Database clients
└── scripts/             # Utility scripts
```

## Prerequisites

- Go 1.24 or higher
- Docker & Docker Compose
- Google Cloud Console account (for OAuth2)
- Google AI Studio account (for Gemini API)

## Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/your-org/Nexus-Pulse-AI.git
cd Nexus-Pulse-AI
```

### 2. Start the database services

```bash
make db-up
```

This starts PostgreSQL and Redis containers.

### 3. Configure environment

Copy the default configuration and update with your credentials:

```bash
# Set environment variables or update config/default.yaml
export OAUTH2__GOOGLE__CLIENT_ID="your-google-client-id"
export OAUTH2__GOOGLE__CLIENT_SECRET="your-google-client-secret"
export OAUTH2__JWT_SECRET="your-secure-jwt-secret"
```

### 4. Run the application

```bash
# Development mode
make run

# Or build and run
make build
./build/nexus-pulse-ai
```

The API will be available at `http://localhost:8088`.

## Docker Deployment

### Start all services

```bash
make docker-up
```

### View logs

```bash
make docker-logs
```

### Stop services

```bash
make docker-down
```

## Available Make Commands

| Command | Description |
|---------|-------------|
| `make build` | Build the application |
| `make build-linux` | Build for Linux (amd64) |
| `make run` | Run the application |
| `make test` | Run tests |
| `make test-cover` | Run tests with coverage report |
| `make lint` | Run linter |
| `make lint-fix` | Run linter with auto-fix |
| `make docker-up` | Start all services |
| `make docker-down` | Stop all services |
| `make docker-logs` | Follow logs for all services |
| `make docker-build` | Build docker images |
| `make db-up` | Start database services only |
| `make db-down` | Stop database services |
| `make deps` | Download and tidy dependencies |
| `make clean` | Remove build artifacts |

## Configuration

Configuration is managed via `config/default.yaml` and environment variables. Environment variables use double underscore (`__`) as separator for nested keys.

### Key Configuration Options

| Environment Variable | Description | Default |
|---------------------|-------------|---------|
| `ENVIRONMENT` | Runtime environment | `development` |
| `SERVER__HTTP__PORT` | HTTP server port | `8088` |
| `POSTGRES__HOST` | PostgreSQL host | `127.0.0.1` |
| `POSTGRES__PORT` | PostgreSQL port | `5432` |
| `POSTGRES__DATABASE` | Database name | `nexus-pulse-ai-db` |
| `POSTGRES__USERNAME` | Database user | `developer` |
| `POSTGRES__PASSWORD` | Database password | `secret` |
| `REDIS__ADDRESS` | Redis address | `127.0.0.1:6379` |
| `OAUTH2__GOOGLE__CLIENT_ID` | Google OAuth2 client ID | - |
| `OAUTH2__GOOGLE__CLIENT_SECRET` | Google OAuth2 client secret | - |
| `OAUTH2__JWT_SECRET` | JWT signing secret | - |
| `OAUTH2__JWT_EXPIRY` | JWT expiry in hours | `24` |
| `OAUTH2__REFRESH_EXPIRY` | Refresh token expiry in days | `30` |

## API Endpoints

### Health Check

```
GET /health
```

### Authentication

```
GET  /api/v1/auth/google/login     # Initiate Google OAuth2 login
GET  /api/v1/auth/google/callback  # OAuth2 callback handler
POST /api/v1/auth/refresh          # Refresh access token
POST /api/v1/auth/logout           # Logout and invalidate tokens
```

### User

```
GET /api/v1/users/me               # Get current user info
```

### Generations (MVP)

```
POST /api/v1/generations/poster    # Create poster generation
GET  /api/v1/generations/:id       # Get generation status/result
GET  /api/v1/generations           # List user's generations
```

## OAuth2 Setup

See [OAUTH2_SETUP.md](OAUTH2_SETUP.md) for detailed instructions on configuring Google OAuth2.

## Architecture

See [ARCHITECTURE.md](ARCHITECTURE.md) for detailed architecture documentation.

## Project Plan

See [PLAN.md](PLAN.md) for the full project roadmap and implementation plan.

## Development

### Running Tests

```bash
make test
```

### Running Tests with Coverage

```bash
make test-cover
```

### Linting

```bash
make lint
```

## Security Notes

- Never commit sensitive credentials to the repository
- Use environment variables for all secrets in production
- JWT tokens are validated against Redis blacklist
- All passwords should be changed from defaults in production

## License

Copyright (c) 2024 Nexus Pulse AI. All rights reserved.
