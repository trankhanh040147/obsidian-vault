# Architecture Overview

This document describes the clean architecture structure of the Nexus Pulse AI project.

## Table of Contents

- [Design Principles](#design-principles)
- [Layered Architecture](#layered-architecture)
- [Directory Structure](#directory-structure)
- [Component Interactions](#component-interactions)
- [Data Flow](#data-flow)
- [Key Components](#key-components)
- [Security Architecture](#security-architecture)
- [Best Practices](#best-practices)

---

## Design Principles

Nexus Pulse AI follows **Clean Architecture** principles:

1. **Dependency Rule**: Dependencies flow inward only. Outer layers can depend on inner layers, but not vice versa.
2. **Separation of Concerns**: Each layer has a single responsibility.
3. **Testability**: Business logic can be tested without external dependencies.
4. **Framework Independence**: The core business logic doesn't depend on frameworks.

```
┌─────────────────────────────────────────────┐
│           Presentation Layer                │  (HTTP Handlers, Middleware)
│  ┌───────────────────────────────────────┐  │
│  │          Use Case Layer               │  │  (Business Logic)
│  │  ┌─────────────────────────────────┐  │  │
│  │  │      Repository Layer           │  │  │  (Data Access Interfaces)
│  │  │  ┌───────────────────────────┐  │  │  │
│  │  │  │   Domain/Models Layer    │  │  │  │  (Entities)
│  │  │  └───────────────────────────┘  │  │  │
│  │  └─────────────────────────────────┘  │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
           ↑ Dependencies flow inward
```

---

## Layered Architecture

### 1. Presentation Layer (`internal/presentation/`)

The outermost layer handling HTTP concerns:

- **HTTP Handlers**: Process requests, validate input, format responses
- **Middleware**: Authentication, logging, rate limiting
- **Router**: Route definitions and grouping

**Key Files:**
- `http/router.go` - Route setup and dependency injection
- `http/handlers.go` - Request handlers
- `http/auth_handlers.go` - Authentication-specific handlers
- `http/middleware.go` - Middleware functions

**Responsibilities:**
- Parse HTTP requests
- Validate request format (not business rules)
- Call use cases
- Format HTTP responses
- Handle HTTP-specific concerns (cookies, headers)

### 2. Use Case Layer (`internal/usecase/`)

Contains all business logic and application rules:

- **Use Cases**: Orchestrate business operations
- **Interfaces**: Define contracts for external dependencies

**Key Files:**
- `usecase.go` - Use case interface and aggregator
- `auth.go` - Authentication business logic

**Responsibilities:**
- Implement business rules
- Coordinate between repositories
- Handle business validation
- Manage transactions when needed

### 3. Repository Layer (`internal/repositories/`)

Defines interfaces for data access:

- **Interfaces**: Abstract data operations
- **Implementations**: Concrete implementations (e.g., PostgreSQL)

**Key Files:**
- `repositories.go` - Repository interface definitions
- `postgres/postgres.go` - PostgreSQL implementation

**Responsibilities:**
- Define data access contracts
- Hide storage implementation details
- Provide data persistence operations

### 4. Domain/Models Layer (`internal/models/`)

Core business entities:

- **Entities**: Business objects with their properties
- **Value Objects**: Immutable objects defined by their attributes

**Key Files:**
- `user.go` - User entity

**Responsibilities:**
- Define business entities
- Contain entity-specific validation
- No external dependencies

---

## Directory Structure

```
.
├── cmd/
│   └── server/
│       └── main.go              # Application entry point
├── config/
│   ├── config.go                # Configuration struct and loader
│   └── default.yaml             # Default configuration values
├── docker/
│   ├── Dockerfile               # Container build instructions
│   └── docker-compose.yaml      # Multi-container setup
├── internal/
│   ├── models/                  # Domain entities
│   │   └── user.go
│   ├── must/                    # Initialization helpers (panic on error)
│   │   ├── postgres.go
│   │   └── redis.go
│   ├── presentation/
│   │   └── http/                # HTTP layer
│   │       ├── auth_handlers.go
│   │       ├── handlers.go
│   │       ├── middleware.go
│   │       └── router.go
│   ├── repositories/
│   │   ├── repositories.go      # Interface definitions
│   │   └── postgres/
│   │       └── postgres.go      # PostgreSQL implementation
│   ├── storage/
│   │   └── storage.go           # File storage abstraction
│   └── usecase/
│       ├── usecase.go           # Use case aggregator
│       └── auth.go              # Auth business logic
├── pkg/                         # Reusable packages
│   ├── cache/
│   │   └── cache.go             # Cache interface and Redis impl
│   ├── constant/
│   │   └── env.go               # Environment constants
│   ├── l/                       # Legacy logger (deprecated)
│   ├── log/                     # Structured logging
│   │   ├── logger.go
│   │   ├── context.go
│   │   └── gorm_logger.go
│   └── storage/
│       ├── postgres.go          # PostgreSQL connection
│       └── redis.go             # Redis connection
├── scripts/
│   └── setup.sh                 # Setup scripts
├── Makefile                     # Build and development commands
├── ARCHITECTURE.md              # This file
├── PLAN.md                      # Project roadmap
└── README.md                    # Project overview
```

---

## Component Interactions

### Request Flow

```
HTTP Request
    │
    ▼
┌─────────────────┐
│     Router      │  ── Routes request to handler
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Middleware    │  ── Auth validation, logging
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│    Handler      │  ── Parse request, call use case
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│    Use Case     │  ── Execute business logic
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Repository    │  ── Data access
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│    Database     │  ── PostgreSQL / Redis
└─────────────────┘
```

### Dependency Injection

Dependencies are injected from `main.go`:

```go
// 1. Infrastructure (Database connections)
db, dbClose := must.ConnectPostgres(cfg.Postgres)
redisClient := must.ConnectRedis(ctx, cfg.Redis)

// 2. Repository (Data access)
repo := postgres.New(appLogger, db, cfg)
cacheSvc := cache.NewRedisCache(redisClient)

// 3. Use Cases (Business logic)
uc := usecase.NewUseCase(repo, cfg, cacheSvc)

// 4. Presentation (HTTP handlers)
httpRouter := httphandler.NewRouter(uc, appLogger, cfg)
```

---

## Data Flow

### Authentication Flow (Google OAuth2)

```
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│  Client  │────▶│  Router  │────▶│ Handler  │────▶│ UseCase  │
└──────────┘     └──────────┘     └──────────┘     └────┬─────┘
                                                        │
                                       ┌────────────────┴────────────────┐
                                       │                                 │
                                       ▼                                 ▼
                                 ┌──────────┐                     ┌──────────┐
                                 │  Google  │                     │   Repo   │
                                 │  OAuth2  │                     │ (User)   │
                                 └──────────┘                     └──────────┘
```

1. Client initiates login → `/api/v1/auth/google/login`
2. Handler generates state, redirects to Google
3. Google authenticates user, redirects back with code
4. Callback handler exchanges code for token
5. Use case fetches user info from Google
6. Repository creates/updates user in database
7. Use case generates JWT + refresh token
8. Handler sets cookies and redirects

### Protected Request Flow

```
Request with JWT
      │
      ▼
┌─────────────────┐
│ AuthMiddleware  │ ── Extract token from header/cookie
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Check Redis    │ ── Is token blacklisted?
│   Blacklist     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Validate JWT   │ ── Signature + expiry
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Get User from  │ ── Load user from DB
│     Token       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Set Context    │ ── c.Set("user", user)
└────────┬────────┘
         │
         ▼
    Handler
```

---

## Key Components

### Use Case Interface Pattern

Use cases are accessed through interfaces:

```go
// Interface definition
type UseCase interface {
    Auth() AuthUseCase
    // Future: Generation() GenerationUseCase
}

// Usage in handlers
func (r *Router) GetCurrentUser(c *gin.Context) {
    user, _ := c.Get("user")
    // Use case accessed via interface
    // r.useCase.Auth().SomeMethod()
}
```

### Repository Interface Pattern

Repositories are defined by interfaces, implemented by concrete types:

```go
// Interface (internal/repositories/repositories.go)
type Repository interface {
    GetUserByGoogleID(ctx context.Context, googleID string) (*models.User, error)
    CreateUser(ctx context.Context, user *models.User) error
    // ...
}

// Implementation (internal/repositories/postgres/postgres.go)
func (q *Queries) GetUserByGoogleID(ctx context.Context, googleID string) (*models.User, error) {
    var user models.User
    err := q.db.WithContext(ctx).Where("google_id = ?", googleID).First(&user).Error
    return &user, err
}
```

### Cache Layer

Redis is used for:

- **Token Blacklist**: Revoked JWT tokens
- **Session Storage**: User sessions (future)
- **Rate Limiting**: Per-user request limits (future)

```go
type Cache interface {
    Set(ctx context.Context, key string, expiry time.Duration, value interface{}) error
    Get(ctx context.Context, key string, dest interface{}) error
    Exists(ctx context.Context, key string) (bool, error)
    Delete(ctx context.Context, keys ...string) error
}
```

---

## Security Architecture

### Authentication

1. **OAuth2 with Google**: Primary authentication method
2. **JWT Access Tokens**: Short-lived (24 hours default)
3. **Refresh Tokens**: Long-lived (30 days default), stored as hashed values
4. **Token Blacklist**: Redis-based revocation for logged-out tokens

### Token Storage

| Token Type | Storage | Lifetime |
|------------|---------|----------|
| Access Token (JWT) | Client (Cookie/Header) | 24 hours |
| Refresh Token | Client + DB (hashed) | 30 days |
| Blacklisted Token | Redis | Until original expiry |

### Cookie Security

```go
http.SetCookie(w, &http.Cookie{
    Name:     "auth_token",
    Value:    token,
    HttpOnly: true,                    // Prevent XSS
    Secure:   env == "production",     // HTTPS only in prod
    SameSite: http.SameSiteLaxMode,    // CSRF protection
    Path:     "/",
})
```

---

## Best Practices

### Context Propagation

Always use request context for cancellation and tracing:

```go
// CORRECT: Use request context
ctx := c.Request.Context()
user, err := uc.repo.GetUserByID(ctx, userID)

// WRONG: Don't use background context in handlers
user, err := uc.repo.GetUserByID(context.Background(), userID)
```

### Error Handling

Wrap errors with context:

```go
// CORRECT
if err != nil {
    return fmt.Errorf("failed to get user by ID %d: %w", userID, err)
}

// WRONG
if err != nil {
    return err  // No context
}
```

### Logging

Use structured logging with context:

```go
// CORRECT
logger.Info("User logged in",
    l.Uint("user_id", user.ID),
    l.String("email", user.Email),
)

// WRONG
logger.Info("User " + user.Email + " logged in")  // String concatenation
```

### Database Queries

Always use context with GORM:

```go
// CORRECT
q.db.WithContext(ctx).Where("id = ?", id).First(&user)

// WRONG
q.db.Where("id = ?", id).First(&user)  // No context
```

---

## Future Extensions

### Planned Components

1. **Generation Module**
   - `internal/models/generation.go` - Generation entity
   - `internal/usecase/generation.go` - AI generation logic
   - `internal/presentation/http/generation_handlers.go` - API handlers

2. **Rate Limiting**
   - `internal/usecase/rate_limit.go` - Rate limit logic
   - `internal/presentation/http/rate_limit_middleware.go` - Middleware

3. **Payment Integration**
   - `internal/usecase/payment.go` - Payment processing
   - `internal/repositories/payment/` - Payment provider implementations

### Adding a New Feature

1. **Define Model** (`internal/models/`)
2. **Add Repository Interface** (`internal/repositories/repositories.go`)
3. **Implement Repository** (`internal/repositories/postgres/`)
4. **Create Use Case** (`internal/usecase/`)
5. **Add Handlers** (`internal/presentation/http/`)
6. **Register Routes** (`internal/presentation/http/router.go`)
