---
title: 'Quick Start'
description: 'Get your Control de Cambios instance running in minutes'
---

# Quick Start

Get Control de Cambios up and running in just a few minutes.

## Run the Application

### Using Maven

```bash
mvn spring-boot:run -Pdev
```

The application will start on `http://localhost:8080`

### Using the JAR File

If you've already built the project:

```bash
java -jar target/control-cambio-dev.jar
```

### Using Docker

```bash
docker-compose up
```

<Check>
  The application is running when you see: "Started SpringBootRunner in X seconds"
</Check>

## Verify Installation

### Check Application Health

```bash
curl http://localhost:8080/actuator/health
```

Expected response:
```json
{
  "status": "UP"
}
```

### Access API Documentation

Open your browser and navigate to:

```
http://localhost:8080/swagger-ui.html
```

You should see the interactive API documentation powered by Swagger/OpenAPI.

## First API Call

### 1. Login (Authentication)

First, obtain an access token by logging in:

```bash
curl -X POST http://localhost:8080/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "admin",
    "password": "admin123"
  }'
```

Response:
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresIn": 86400,
  "user": {
    "id": 1,
    "username": "admin",
    "email": "admin@vortexbird.com"
  }
}
```

<Note>
  Save the token value - you'll need it for authenticated requests.
</Note>

### 2. Make an Authenticated Request

Use the token to fetch projects:

```bash
curl -X GET http://localhost:8080/api/v1/proyecto/encontrarTodosProyectos \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### 3. Create a Change Control Record

```bash
curl -X POST http://localhost:8080/api/v1/registroControl/guardarRegistroControl \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "proyId": 1,
    "recoDescripcion": "Implementación de nueva funcionalidad",
    "recoFechaSolicitud": "2024-02-03",
    "recoEstado": "PENDIENTE"
  }'
```

## Common Operations

### List All Projects

```bash
curl -X GET http://localhost:8080/api/v1/proyecto/encontrarTodosProyectos \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### Get Project by ID

```bash
curl -X GET http://localhost:8080/api/v1/proyecto/encontrarProyecto/1 \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### List Developers

```bash
curl -X GET http://localhost:8080/api/v1/desarrollador/encontrarDesarrolladores \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### Query Hour Pool (Bolsa de Horas)

```bash
curl -X POST http://localhost:8080/api/v1/bolsa-horas/consultaBolsaHoras \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "proyId": 1
  }'
```

### Generate PDF Report

```bash
curl -X GET http://localhost:8080/api/v1/registroControl/descargarRegistroControl/1 \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  --output reporte.pdf
```

## Development Workflow

### 1. Make Code Changes

Edit your Java files in `src/main/java/com/vortexbird/control/cambio/`

### 2. Rebuild and Restart

With Spring Boot DevTools enabled, the application will automatically reload on code changes.

For manual restart:

```bash
mvn spring-boot:run -Pdev
```

### 3. Run Tests

```bash
# Run all tests
mvn test

# Run specific test class
mvn test -Dtest=CcRegistroControlServiceTest

# Run with coverage
mvn test jacoco:report
```

### 4. Check Code Quality

```bash
# Run mutation testing
mvn test pitest:mutationCoverage
```

## Troubleshooting

### Port Already in Use

If port 8080 is already in use, change it in `application.properties`:

```properties
server.port=8081
```

### Database Connection Failed

1. Verify PostgreSQL is running:
```bash
pg_isready
```

2. Check database credentials in configuration file

3. Ensure database exists:
```bash
psql -U postgres -c "\l" | grep control_cambio
```

### Application Won't Start

1. Check Java version:
```bash
java -version
```
Should be Java 17 or higher

2. Clear Maven cache and rebuild:
```bash
mvn clean install -U
```

3. Check logs in console output for specific errors

## Next Steps

<CardGroup cols={2}>
  <Card title="API Reference" icon="book" href="/api-reference/authentication">
    Explore all available API endpoints
  </Card>
  <Card title="Architecture" icon="sitemap" href="/architecture/overview">
    Learn about the system architecture
  </Card>
  <Card title="Security" icon="shield" href="/architecture/security">
    Understand authentication and authorization
  </Card>
  <Card title="Deployment" icon="rocket" href="/guides/deployment">
    Deploy to production
  </Card>
</CardGroup>
