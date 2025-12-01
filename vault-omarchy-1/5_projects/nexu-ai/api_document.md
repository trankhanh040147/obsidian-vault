# API Documentation

  

## Base URL

  

```

Development: http://localhost:8088

Production: https://api.yourdomain.com

```

  

## Authentication Flow Overview

  

The authentication system uses Google OAuth2 with JWT tokens and refresh tokens:

  

1. **Login**: User initiates Google OAuth login

2. **Callback**: Backend handles OAuth callback and returns tokens

3. **Token Storage**: Frontend stores access token and refresh token

4. **API Requests**: Include access token in requests

5. **Token Refresh**: Use refresh token when access token expires

6. **Logout**: Invalidate tokens and clear storage

  

---

  

## Endpoints

  

### 1. Health Check

  

Check if the API is running.

  

**Endpoint:** `GET /health`

  

**Request:**

```http

GET /health HTTP/1.1

Host: localhost:8088

```

  

**Response:** `200 OK`

```json

{

"status": "ok"

}

```

  

---

  

### 2. Initiate Google Login

  

Initiates the Google OAuth2 login flow. Returns a URL that should be opened in a browser.

  

**Endpoint:** `GET /api/v1/auth/google/login`

  

**Request:**

```http

GET /api/v1/auth/google/login HTTP/1.1

Host: localhost:8088

```

  

**Response:** `200 OK`

```json

{

"url": "https://accounts.google.com/o/oauth2/v2/auth?client_id=...&redirect_uri=...&response_type=code&scope=..."

}

```

  

**Note:** After user authenticates with Google, they will be redirected to `/api/v1/auth/google/callback` with `code` and `state` parameters.

  

---

  

### 3. Google OAuth Callback

  

Handles the OAuth callback from Google. This endpoint is typically called automatically by Google's redirect, but you can also call it manually with the authorization code.

  

**Endpoint:** `GET /api/v1/auth/google/callback`

  

**Query Parameters:**

- `code` (required): Authorization code from Google

- `state` (required): State parameter for CSRF protection

  

**Request:**

```http

GET /api/v1/auth/google/callback?code=AUTHORIZATION_CODE&state=STATE_VALUE HTTP/1.1

Host: localhost:8088

Cookie: oauth_state=STATE_VALUE

```

  

**Response:** `200 OK`

```json

{

"message": "Login successful",

"user": {

"id": 1,

"email": "user@example.com",

"name": "John Doe"

},

"access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",

"refresh_token": "dGhpcyBpcyBhIHJlZnJlc2ggdG9rZW4...",

"redirect_url": "http://localhost:3000"

}

```

  

**Note:** Tokens are also set in HTTP-only cookies (`auth_token` and `refresh_token`), so they're automatically included in subsequent requests if `credentials: 'include'` is used.

  

---

  

### 4. Refresh Access Token

  

Refreshes an expired access token using a refresh token.

  

**Endpoint:** `POST /api/v1/auth/refresh`

  

**Headers:**

- `X-Refresh-Token` (optional): Refresh token (can also be sent as cookie)

  

**Request:**

```http

POST /api/v1/auth/refresh HTTP/1.1

Host: localhost:8088

X-Refresh-Token: dGhpcyBpcyBhIHJlZnJlc2ggdG9rZW4...

```

  

**Or using cookie:**

```http

POST /api/v1/auth/refresh HTTP/1.1

Host: localhost:8088

Cookie: refresh_token=dGhpcyBpcyBhIHJlZnJlc2ggdG9rZW4...

```

  

**Response:** `200 OK`

```json

{

"access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",

"refresh_token": "bmV3IHJlZnJlc2ggdG9rZW4..."

}

```

  

**Note:** The refresh token may be rotated (new refresh token returned) for security.

  

---

  

### 5. Get Current User

  

Returns the current authenticated user. Requires a valid access token.

  

**Endpoint:** `GET /api/v1/me`

  

**Headers:**

- `Authorization: Bearer <access_token>` (required, or use `auth_token` cookie)

  

**Request:**

```http

GET /api/v1/me HTTP/1.1

Host: localhost:8088

Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

```

  

**Response:** `200 OK`

```json

{

"user": {

"id": 1,

"email": "user@example.com",

"name": "John Doe",

"picture": "https://lh3.googleusercontent.com/...",

"email_verified": true,

"created_at": "2024-01-01T00:00:00Z",

"updated_at": "2024-01-01T00:00:00Z"

}

}

```

  

---

  

### 6. Logout

  

Logs out the current user and invalidates tokens.

  

**Endpoint:** `POST /api/v1/auth/logout`

  

**Headers:**

- `Authorization: Bearer <access_token>` (optional, can also use cookie)

  

**Request:**

```http

POST /api/v1/auth/logout HTTP/1.1

Host: localhost:8088

Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

```

  

**Response:** `200 OK`

```json

{

"message": "Logout successful"

}

```

  

**Note:**

- Blacklists the access token until it expires

- Revokes the refresh token

- Clears auth cookies

  

---

  

## Error Responses

  

All endpoints may return the following error responses:

  

### 400 Bad Request

```json

{

"error": "Invalid state"

}

```

  

### 401 Unauthorized

```json

{

"error": "Invalid or expired token"

}

```

  

### 500 Internal Server Error

```json

{

"error": "Failed to complete authentication"

}

```

  

---

  

## Token Management

  

### Access Token

- **Type**: JWT (JSON Web Token)

- **Expiry**: 7 days (configurable)

- **Storage**:

- HTTP-only cookie: `auth_token` (automatically set by backend)

- Can be stored in localStorage for manual management

- **Usage**: Include in `Authorization: Bearer <token>` header

  

### Refresh Token

- **Type**: Random string (stored in Redis)

- **Expiry**: 30 days (configurable)

- **Storage**:

- HTTP-only cookie: `refresh_token` (automatically set by backend)

- Can be stored in localStorage for manual management

- **Usage**: Send in `X-Refresh-Token` header or as cookie

  

### Token Refresh Flow

  

When an access token expires (401 Unauthorized response):

1. Extract refresh token from storage or cookie

2. Call `POST /api/v1/auth/refresh` with refresh token

3. Update stored access token with new token

4. If new refresh token is returned, update refresh token as well

5. Retry the original request with new access token

  

---

  

## Security Best Practices

  

1. **Use HTTP-only Cookies**: Tokens are automatically set in HTTP-only cookies by the backend (recommended)

2. **HTTPS Only**: Always use HTTPS in production

3. **Token Refresh**: Implement automatic token refresh when receiving 401 responses

4. **Error Handling**: Handle 401 errors gracefully and redirect to login if refresh fails

5. **CSRF Protection**: The backend uses SameSite cookies for CSRF protection

6. **Token Storage**: Never log or expose tokens in client-side code

  

---

  

## CORS Configuration

  

The API supports CORS with the following configuration:

- **Allowed Origins**: All origins (configure for production)

- **Allowed Methods**: GET, POST, PUT, PATCH, DELETE, OPTIONS

- **Allowed Headers**: Authorization, Content-Type, X-Refresh-Token, etc.

- **Credentials**: Enabled (cookies are sent)

  

**Important:** Include `credentials: 'include'` in fetch requests to send cookies:

  

```http

GET /api/v1/me HTTP/1.1

Host: localhost:8088

Cookie: auth_token=...

```

  

---

  

## Request/Response Examples

  

### Login Flow

  

1. **Initiate Login:**

```http

GET /api/v1/auth/google/login

```

Response contains `url` - redirect user to this URL

  

2. **User authenticates with Google** (happens in browser)

  

3. **Google redirects to callback:**

```http

GET /api/v1/auth/google/callback?code=CODE&state=STATE

```

Response contains `access_token` and `refresh_token`

  

4. **Store tokens** and use `access_token` for authenticated requests

  

### Authenticated Request

  

```http

GET /api/v1/me HTTP/1.1

Host: localhost:8088

Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

```

  

### Token Refresh

  

```http

POST /api/v1/auth/refresh HTTP/1.1

Host: localhost:8088

X-Refresh-Token: dGhpcyBpcyBhIHJlZnJlc2ggdG9rZW4...

```

  

### Logout

  

```http

POST /api/v1/auth/logout HTTP/1.1

Host: localhost:8088

Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

```

  

---

  

## Troubleshooting

  

### Token Expired (401 Unauthorized)

- **Symptom**: API returns 401 Unauthorized

- **Solution**: Call refresh token endpoint to get new access token

  

### CORS Errors

- **Symptom**: CORS policy errors in browser console

- **Solution**: Ensure `credentials: 'include'` is set in fetch options

  

### State Mismatch

- **Symptom**: "Invalid state" error on callback

- **Solution**: Ensure cookies are enabled and SameSite is supported by browser

  

### Refresh Token Expired

- **Symptom**: Refresh endpoint returns 401

- **Solution**: Redirect user to login page to re-authenticate

  

---

  

## Support

  

For issues or questions, please contact the backend team or refer to the backend repository.