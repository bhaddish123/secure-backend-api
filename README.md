# secure-backend-api

A Spring Boot REST API that demonstrates JWT authentication, BCrypt password hashing, protected routes, and PostgreSQL. It is the backend half of the "Nimbus Core" style project described on my profile: a small, production-oriented auth service with a documented production config example.

This repository is the API and a `/health` + `/protected` check. It is not a full product UI.

---

## Tech stack

- Java 17
- Spring Boot 4.0.1 (`spring-boot-starter-webmvc`, Data JPA, Spring Security)
- PostgreSQL (runtime driver; `application-prod.example.yml` shows `sslmode=require` for RDS-style hosts)
- JJWT 0.11.5 (HS256)
- BCrypt via `BCryptPasswordEncoder`

Related work: I also implemented the backend for [Nimbus Core](https://github.com/kizzycpt/nimbus-core) (JWT auth, PostgreSQL, protected routes) in collaboration with a teammate who owned infrastructure.

---

## What the API does

| Method | Path | Auth | Behavior |
|---|---|---|---|
| `GET` | `/health` | public | Liveness check |
| `POST` | `/register` | public | Creates a user. The password is hashed with BCrypt before it is saved. Returns `UserResponse` (`id`, `username`) — not the password. |
| `POST` | `/login` | public | Verifies the password with `PasswordEncoder.matches`. On success, returns a JWT (1-hour expiry) as a plain string body. `401` if the credentials do not match. |
| `GET` | `/protected` | Bearer JWT | Requires a valid token. Used to confirm the auth filter is working. |

`SecurityConfig` permits `/health`, `/register`, and `/login`, and requires authentication for everything else.

---

## Project layout

```
secure-backend-api/
├── pom.xml
├── mvnw
└── src/main/
    ├── java/com/bereket/secure_api/
    │   ├── SecureApiApplication.java
    │   ├── controller/HealthController.java   # /health, /protected
    │   ├── user/                              # User entity, repository, login/register
    │   ├── jwt/                               # JwtUtil, JwtAuthFilter
    │   └── security/SecurityConfig.java
    └── resources/
        ├── application.properties             # spring.application.name only
        └── application-prod.example.yml       # datasource + jwt.secret template
```

---

## Running locally

**Prerequisites:** Java 17, Maven (`./mvnw`), and a PostgreSQL database for anything beyond a quick compile.

```bash
./mvnw spring-boot:run
```

### Production-style config

Copy `src/main/resources/application-prod.example.yml` and fill in real values. Do not commit them.

```yaml
spring:
  datasource:
    url: jdbc:postgresql://<RDS_ENDPOINT>:5432/<DB_NAME>?sslmode=require
    username: <DB_USERNAME>
    password: <DB_PASSWORD>
jwt:
  secret: <JWT_SECRET>
server:
  port: 8080
```

### Quick demo

```bash
# Register
curl -X POST http://localhost:8080/register \
  -H "Content-Type: application/json" \
  -d '{"username":"demo","password":"password123"}'

# Login (response body is the JWT)
curl -X POST http://localhost:8080/login \
  -H "Content-Type: application/json" \
  -d '{"username":"demo","password":"password123"}'

# Protected endpoint
curl http://localhost:8080/protected \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

---

## Config notes (accurate to the current code)

- `JwtUtil` currently signs tokens with a **hardcoded placeholder** string (`change-this-secret-later-...`), not the `jwt.secret` value in `application-prod.example.yml`. Before any shared or production deployment, that key should be loaded from configuration and the placeholder removed.
- Tokens expire one hour after issue.
- `application.properties` in the repo only sets `spring.application.name=secure-api`; datasource settings need to be supplied by a profile or environment.

---

## License

Personal / portfolio project.
