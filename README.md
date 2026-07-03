# Auth Workshop — Python Flask JWT & OAuth2

An educational project demonstrating authentication and authorization in Flask using JWT and RBAC.

> 🇺🇦 Українська версія: [README_UA.md](README_UA.md)

## Tech Stack
- **Python ≥ 3.12**
- **Flask** — web framework
- **PyJWT** — JSON Web Tokens
- **python-dotenv** — environment variable management
- **RBAC** — Role-Based Access Control (roles: admin/user)

## Project Structure
```
flask-jwt-auth/
├── src/
│   ├── app.py                # Main application file
│   ├── users.py              # User store (in-memory)
│   ├── auth_middleware.py    # JWT verification middleware
│   └── role_middleware.py    # Role verification middleware
├── .env                      # Environment variables (create manually)
├── pyproject.toml            # Project metadata
└── README.md
```

## Setup & Run

### 1. Create a virtual environment
```bash
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
# .venv\Scripts\activate   # Windows
```

### 2. Install dependencies
```bash
pip install Flask PyJWT python-dotenv
```

### 3. Configure the .env file
Create a `.env` file in the project root:
```env
JWT_SECRET=your-super-secret-key-change-me
```
> Without this variable the app will not start (checked on startup).

### 4. Run the server
```bash
python src/app.py
```

The server starts on `http://localhost:3000`

## API Endpoints

### POST /login
Authenticate a user and obtain a JWT token.

**Available accounts:**
- **User:** `user@example.com` / `user123` (role: user)
- **Admin:** `admin@example.com` / `admin123` (role: admin)

**Response:**
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "token_type": "Bearer",
  "expires_in": 900
}
```

### GET /profile
Get the current user's profile (JWT token required).

### DELETE /users/:id
Delete a user (admin role only).

## Usage Examples

### 1. Log in as a regular user
```bash
curl -X POST http://localhost:3000/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"user123"}'
```

### 2. Log in as an admin
```bash
curl -X POST http://localhost:3000/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"admin123"}'
```

### 3. Get profile without a token (401 Unauthorized)
```bash
curl -i http://localhost:3000/profile
```

### 4. Get profile with a token
```bash
curl http://localhost:3000/profile \
  -H "Authorization: Bearer <YOUR_TOKEN>"
```

### 5. Attempt deletion as a user (403 Forbidden)
```bash
curl -i -X DELETE http://localhost:3000/users/5 \
  -H "Authorization: Bearer <USER_TOKEN>"
```

### 6. Deletion as an admin (200 OK)
```bash
curl -X DELETE http://localhost:3000/users/5 \
  -H "Authorization: Bearer <ADMIN_TOKEN>"
```

## OAuth2 Demo (Google)

> ⚠️ This is a **manual demonstration** of the OAuth2 flow — there is no OAuth2 code in the project itself.

Demonstrating OAuth2 via Google OAuth Playground:

1. Open the [Google OAuth Playground](https://developers.google.com/oauthplayground/)
2. Select the required scopes:
   - `https://www.googleapis.com/auth/userinfo.email`
   - `https://www.googleapis.com/auth/userinfo.profile`
3. Click **Authorize APIs** → get an authorization code
4. **Exchange authorization code for tokens** → get an access token
5. Use the token to fetch user data:
   ```bash
   curl -H "Authorization: Bearer <GOOGLE_ACCESS_TOKEN>" \
     https://www.googleapis.com/oauth2/v2/userinfo
   ```

## Security

⚠️ **IMPORTANT:** This is an educational project!

- Passwords are stored in plaintext (use bcrypt/argon2 in production)
- The JWT secret must be strong and unique (never commit `.env` to git)
- Users are stored in memory (use a database in production)
- `debug=True` is enabled for development only — disable it in production
- Do not use this code in production without proper security changes!

## Implementation Notes

- **JWT tokens** expire after 15 minutes (expires_in: 900 seconds)
- **Middleware decorators** for authentication and authorization
- **RBAC** — role checks via the `@check_role()` decorator
- **Debug logs** for tracing JWT errors (see the server console)
