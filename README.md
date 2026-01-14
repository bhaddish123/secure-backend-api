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

## Endpoints
- POST /register — create a new user
- POST /login — authenticate and receive a JWT
- GET /protected — secured endpoint (requires JWT)

## Purpose
This project demonstrates how a real-world backend handles authentication,
security, and cloud database connectivity.
