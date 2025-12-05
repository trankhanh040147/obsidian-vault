# Nexus Pulse AI 

## Current Branch: feats/auth

## Completed
- [x] Google OAuth2 login flow
- [x] JWT access tokens (configurable expiry)
- [x] Refresh tokens stored in PostgreSQL (30 days default)
- [x] Token blacklisting on logout (Redis)
- [x] HTTP-only cookies with SameSite protection
- [x] Context propagation throughout request chain
- [x] Cookie fix: multiple Set-Cookie headers using `c.Writer.Header().Add()`
- [x] API documentation
- [x] Makefile with build/test/lint/docker commands
- [x] golangci-lint configuration
- [x] Migrate refresh tokens from Redis to PostgreSQL
  - RefreshToken model with UserID, TokenHash (SHA256), ExpiresAt
  - Repository methods: Create, GetByHash, Delete, DeleteExpired
  - Auth use case updated to use DB instead of Redis

## In Progress
- [ ] Setup air (hot reload)

## Planned
- [ ] "Logout all devices" endpoint (query by user_id)
- [ ] Cleanup job for expired refresh tokens (use DeleteExpiredRefreshTokens)
- [ ] Unit tests for auth use case
- [ ] Integration tests
- [ ] Rate limiting

## Architecture Decisions

### Refresh Token Storage (PostgreSQL)
**Why database over Redis:**
- Persistent across restarts
- Query all user tokens (logout all devices)
- Audit trail
- Store device metadata (future)

**Redis still used for:**
- JWT blacklisting (short-lived, OK to lose)
### Token Flow
1. Login → JWT (7 days) + Refresh Token (30 days, DB)
2. Refresh → New JWT + New Refresh Token (rotation)
3. Logout → Blacklist JWT (Redis) + Revoke Refresh Token (DB)