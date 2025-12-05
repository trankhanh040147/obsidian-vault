# Nexus Pulse AI - Project Plan

## Executive Summary

Nexus Pulse AI is an AI-powered content creation platform targeting content creators, F&B businesses, and marketers in Vietnam and globally. The platform leverages Google's Gemini model to generate marketing assets including posters, menus, and product designs.

---

## 1. Business Objectives

**Primary Goal**: Provide AI-powered visual content generation tools for:

- Content creators needing quick marketing assets
- F&B businesses requiring menu and promotional materials
- Small businesses needing affordable design solutions

**Target Market**:

- Primary: Vietnamese SMBs, restaurants, cafes, content creators
- Secondary: Global users via PayPal integration

---

## 2. Feature Roadmap

### Phase 1: MVP (Poster/Ads Creation)

- AI-powered poster and advertisement generation
- Multiple style templates (modern, vintage, minimalist, etc.)
- Custom text overlay and branding options
- Export in multiple formats (PNG, JPG, PDF)
- Google OAuth2 authentication
- Basic rate limiting per user

### Phase 2: Menu Creation

- F&B menu generation with food photography enhancement
- Multiple menu layouts (single page, multi-page, digital)
- Price list integration
- QR code generation for digital menus

### Phase 3: Product Design

- Product packaging design
- Product illustration and mockups
- Food photography enhancement
- E-commerce ready exports

### Phase 4: Advanced Features (Future)

- AI voice generation with custom tones
- Video shorts generation
- News/article content generation
- Collaboration features

---

## 3. Technical Architecture

### 3.1 Tech Stack

| Component | Technology           | Notes                                          |
| --------- | -------------------- | ---------------------------------------------- |
| Backend   | Go (Gin framework)   | Clean architecture - already set up            |
| Frontend  | React                | Separate repository                            |
| Database  | PostgreSQL           | User data, generation history                  |
| Cache     | Redis                | Sessions, rate limiting, job queues            |
| AI Model  | Google Gemini API    | Image generation capabilities                  |
| Storage   | Local filesystem     | Initial phase, migrate to S3-compatible later  |
| Auth      | Google OAuth2        | Already implemented                            |

### 3.2 Backend Structure (Existing)

```
internal/
├── models/          # Domain entities
├── presentation/    # HTTP handlers, middleware
├── repositories/    # Data access layer
├── usecase/         # Business logic
└── storage/         # File storage abstraction
```

### 3.3 New Components Required

**Models to add:**

- `Generation` - tracks AI generation requests and results
- `RateLimit` - user rate limit tracking
- `Template` - predefined design templates

**Use Cases to add:**

- `GenerationUseCase` - handle AI generation workflow
- `RateLimitUseCase` - enforce usage limits
- `TemplateUseCase` - manage design templates

**API Endpoints (MVP):**

```
POST /api/v1/generations/poster    # Create poster generation
GET  /api/v1/generations/:id       # Get generation status/result
GET  /api/v1/generations           # List user's generations
GET  /api/v1/templates             # List available templates
GET  /api/v1/users/me/limits       # Get user's rate limit status
```

---

## 4. Infrastructure

### 4.1 Development Environment (First 30 days)

- **VPS**: VN Cloud provider
- **Specs**: 4 cores, 8GB RAM
- **Components**: Nginx + Docker (backend, PostgreSQL, Redis)
- **Domain**: GoDaddy

### 4.2 Production Environment (After 30 days)

- **VPS**: VN Cloud provider (upgraded)
- **Specs**: 8 cores, 16GB RAM
- **Additional**: Separate database server consideration

### 4.3 Docker Services

```yaml
services:
  nginx:    # reverse proxy, SSL termination
  api:      # Go backend
  postgres: # database
  redis:    # cache/sessions
```

---

## 5. Rate Limiting Strategy

**Initial Implementation (MVP):**

- Per-user daily generation limits stored in Redis
- Configurable limits via environment variables
- Soft limits with warnings before hard cutoff

**Structure:**

```
Key: rate_limit:user:{user_id}:daily
Value: { "count": 5, "reset_at": "2024-01-02T00:00:00Z" }
```

**Default Limits:**

- Free tier: 5 generations/day
- (Paid tiers to be defined later)

---

## 6. Payment Integration (Phase 2+)

### Vietnam Users

- **Provider**: [PayOS](https://payos.vn/docs/api/)
- **Methods**: Bank transfer, e-wallets (MoMo, ZaloPay)

### Global Users

- **Provider**: [PayPal](https://www.paypal.com/)
- **Methods**: Credit/debit cards, PayPal balance

**Implementation Notes:**

- Abstract payment interface for multiple providers
- Webhook handlers for payment confirmation
- Transaction logging for reconciliation

---

## 7. Marketing Strategy

### Channels

1. **Social Media** (organic)
   - YouTube: Tutorial videos, feature showcases
   - Facebook: Vietnamese market engagement
   - TikTok: Quick demos, before/after reveals

2. **Paid Advertising**
   - Facebook Ads: Targeting Vietnamese SMBs
   - TikTok Ads: Content creator audience
   - Budget: Research required for cost estimation

### Launch Strategy

1. Soft launch with limited beta users
2. Collect feedback and iterate
3. Public launch with social media campaign
4. Referral program for organic growth

---

## 8. Security Considerations

- JWT token validation with Redis blacklist (already implemented)
- Rate limiting to prevent API abuse
- Input sanitization for AI prompts
- Secure file storage with access controls
- HTTPS enforcement via Nginx
- Environment-based configuration (no hardcoded secrets)

---

## 9. Monitoring & Observability

- Structured logging (already using `pkg/log`)
- Health check endpoints
- Generation success/failure metrics
- Rate limit hit tracking
- API latency monitoring

---

## 10. Implementation Priorities

### Immediate (MVP)

1. Integrate Gemini API for image generation
2. Create Generation model and repository
3. Implement poster generation use case
4. Add rate limiting middleware
5. Build generation API endpoints
6. Basic admin dashboard for monitoring

### Short-term (Post-MVP)

1. Template system for design styles
2. Generation history and gallery
3. Image editing/refinement features
4. Menu generation feature

### Medium-term

1. Payment integration (PayOS + PayPal)
2. Subscription tier system
3. Product design features
4. Performance optimization

---

## 11. Open Questions / Decisions Needed

1. **Gemini API**: Confirm exact model version and API access
2. **Storage Migration**: Timeline for moving from local to cloud storage
3. **Pricing Structure**: Define subscription tiers and pricing
4. **Frontend Repository**: Create and set up React project
5. **Domain Setup**: Configure DNS and SSL certificates

