---
title: 'Configuration'
description: 'Configure Control de Cambios for different environments'
---

# Configuration

Control de Cambios supports multiple environment profiles: `dev`, `test`, and `prod`.

## Environment Profiles

The application uses Spring Boot profiles to manage different configurations. The active profile is set in `application.properties`:

```properties
spring.profiles.active=prod
```

### Available Profiles

- **dev**: Development environment with debug logging and H2 in-memory database support
- **test**: Testing environment for integration tests
- **prod**: Production environment with optimized settings

## Configuration Files

Configuration files are located in `src/main/resources/`:

- `application.properties` - Main configuration file
- `application-dev.properties` - Development-specific settings
- `application-test.properties` - Test-specific settings
- `application-prod.properties` - Production-specific settings

## Database Configuration

### Development (application-dev.properties)

```properties
# Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/control_cambio_dev
spring.datasource.username=cc_user
spring.datasource.password=dev_password
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
```

### Production (application-prod.properties)

```properties
# Database Configuration
spring.datasource.url=${DATABASE_URL}
spring.datasource.username=${DATABASE_USERNAME}
spring.datasource.password=${DATABASE_PASSWORD}
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
```

<Warning>
  In production, always use environment variables for sensitive data like database credentials.
</Warning>

## Security Configuration

### JWT Settings

Configure JWT token settings:

```properties
# JWT Configuration
jwt.secret=${JWT_SECRET}
jwt.expiration=86400000
jwt.refresh-expiration=604800000
```

<Note>
  JWT secret should be a strong, randomly generated string. Never commit secrets to version control.
</Note>

### SSO Integration

Configure Single Sign-On integration:

```properties
# SSO Configuration
sso.base-url=${SSO_BASE_URL}
sso.client-id=${SSO_CLIENT_ID}
sso.client-secret=${SSO_CLIENT_SECRET}
```

## Server Configuration

```properties
# Server Configuration
server.port=8080
server.servlet.context-path=/
server.compression.enabled=true
```

## Logging Configuration

### Development

```properties
# Logging
logging.level.root=INFO
logging.level.com.vortexbird.control.cambio=DEBUG
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate.SQL=DEBUG
```

### Production

```properties
# Logging
logging.level.root=WARN
logging.level.com.vortexbird.control.cambio=INFO
logging.file.name=/var/log/control-cambio/application.log
logging.file.max-size=10MB
logging.file.max-history=30
```

## API Documentation (Swagger/OpenAPI)

```properties
# SpringDoc OpenAPI
springdoc.api-docs.path=/api-docs
springdoc.swagger-ui.path=/swagger-ui.html
springdoc.swagger-ui.operations-sorter=method
springdoc.swagger-ui.tags-sorter=alpha
```

Access Swagger UI at: `http://localhost:8080/swagger-ui.html`

## JasperReports Configuration

Configure report generation settings:

```properties
# Reports Configuration
reports.output-path=/tmp/reports
reports.template-path=classpath:report/
```

## CORS Configuration

For frontend integration, configure CORS:

```properties
# CORS Configuration
cors.allowed-origins=${ALLOWED_ORIGINS:http://localhost:3000}
cors.allowed-methods=GET,POST,PUT,DELETE,OPTIONS
cors.allowed-headers=*
cors.allow-credentials=true
```

## Connection Pool Configuration

Optimize database connection pooling:

```properties
# HikariCP Configuration
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
```

## Environment Variables

For production deployment, set the following environment variables:

| Variable | Description | Example |
|----------|-------------|---------|
| `DATABASE_URL` | PostgreSQL connection URL | `jdbc:postgresql://localhost:5432/control_cambio` |
| `DATABASE_USERNAME` | Database username | `cc_user` |
| `DATABASE_PASSWORD` | Database password | `secure_password` |
| `JWT_SECRET` | JWT signing secret | `your-secret-key-min-256-bits` |
| `SSO_BASE_URL` | SSO service base URL | `https://sso.vortexbird.com` |
| `SSO_CLIENT_ID` | SSO client identifier | `control-cambio` |
| `SSO_CLIENT_SECRET` | SSO client secret | `client-secret` |
| `ALLOWED_ORIGINS` | CORS allowed origins | `https://app.vortexbird.com` |

## Testing Configuration

For running tests, use the test profile:

```bash
mvn test -Ptest
```

The test profile typically uses an H2 in-memory database for faster test execution.

## Next Steps

<Card title="Quick Start" icon="rocket" href="/getting-started/quick-start">
  Run the application with your configuration
</Card>
