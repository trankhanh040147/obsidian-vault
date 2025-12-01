**Implement rate limit middleware:**
- Use **ulule/limiter/v3**
- Use ENV to config rate limit rates
- Example:
```go
import (
	"net/http"
	"strings"
	"time"

	"github.com/gin-gonic/gin"
	"github.com/ulule/limiter/v3"
	"github.com/ulule/limiter/v3/drivers/store/memory"
	l "github.com/nexus-pulse-ai/pkg/log"
)

// RateLimitMiddleware creates a rate limiter middleware
func (r *Router) RateLimitMiddleware(limit string) gin.HandlerFunc {
	// Parse limit string (e.g., "10-M" for 10 requests per minute)
	rate, err := limiter.NewRateFromFormatted(limit)
	if err != nil {
		r.logger.Error("Invalid rate limit format", l.Error(err))
		// Fallback to default: 10 requests per minute
		rate = limiter.Rate{
			Period: time.Minute,
			Limit:  10,
		}
	}

	// Use in-memory store (for production, use Redis store)
	store := memory.NewStore()
	instance := limiter.New(store, rate)

	return func(c *gin.Context) {
		// Get client identifier (IP address)
		clientIP := c.ClientIP()
		
		// Get limiter context
		context, err := instance.Get(c.Request.Context(), clientIP)
		if err != nil {
			r.logger.Error("Rate limiter error", l.Error(err))
			c.JSON(http.StatusInternalServerError, gin.H{
				"error": "Rate limiter error",
			})
			c.Abort()
			return
		}

		// Set rate limit headers
		c.Header("X-RateLimit-Limit", string(rune(context.Limit)))
		c.Header("X-RateLimit-Remaining", string(rune(context.Remaining)))
		c.Header("X-RateLimit-Reset", string(rune(context.Reset)))

		// Check if limit exceeded
		if context.Reached {
			r.logger.Warn("Rate limit exceeded",
				l.String("ip", clientIP),
				l.String("path", c.Request.URL.Path),
			)
			c.JSON(http.StatusTooManyRequests, gin.H{
				"error": "Too many requests. Please try again later.",
				"retry_after": context.Reset,
			})
			c.Abort()
			return
		}

		c.Next()
	}
}

```
---
