# Secure Backend API

A secure backend API built with Spring Boot that demonstrates:
- User registration with hashed passwords
- Authentication using JWT (JSON Web Tokens)
- Protected API endpoints
- PostgreSQL database integration (AWS RDS)
- Environment-based configuration (dev vs prod)

## Tech Stack
- Java / Spring Boot
- Spring Security
- JWT
- PostgreSQL (AWS RDS)
- Maven

## Running Locally

This project uses environment-based configuration.  
Sensitive values (database credentials, JWT secret) are **not committed**.

### Prerequisites
- Java 17+
- Maven
- PostgreSQL (local or cloud)

### Steps
1. Create `application-prod.yml` based on `application-prod.example.yml`
2. Fill in your own database credentials and JWT secret
3. Run the application:

```bash
SPRING_PROFILES_ACTIVE=prod ./mvnw spring-boot:run
```

## Endpoints
- POST /register — create a new user
- POST /login — authenticate and receive a JWT
- GET /protected — secured endpoint (requires JWT)

## Security
- Passwords are hashed using BCrypt
- Authentication is handled via JWT (stateless)
- Protected routes require a valid JWT
- Sensitive configuration (DB credentials, JWT secret) is managed via environment variables and excluded from source control.

## Authentication (JWT)
This API uses JSON Web Tokens (JWT) for stateless authentication.

1. A user logs in with a username and password.
2. The server verifies the credentials and generates a signed JWT.
3. The token is returned to the client.
4. For protected routes, the client sends the token in the request header:
   
   Authorization: Bearer <JWT>

5. The server validates the token’s signature and expiration before allowing access.

JWT allows the API to remain stateless, scalable, and secure without storing session data on the server.

## Purpose
This project demonstrates how a real-world backend handles authentication,
security, and cloud database connectivity.

## Quick Demo

```bash
# Register
curl -X POST http://localhost:8080/register \
-H "Content-Type: application/json" \
-d '{"username":"demo","password":"password123"}'

# Login (returns JWT)
curl -X POST http://localhost:8080/login \
-H "Content-Type: application/json" \
-d '{"username":"demo","password":"password123"}'

# Access protected endpoint
curl http://localhost:8080/protected \
-H "Authorization: Bearer <JWT_TOKEN>"
