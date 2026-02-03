---
title: 'Installation'
description: 'How to install and set up Control de Cambios'
---

# Installation

This guide will walk you through the installation process for Control de Cambios.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Java 17** or higher
- **Maven 3.6+** for building the project
- **PostgreSQL 12+** for the database
- **Docker** (optional, for containerized deployment)

### Verify Java Installation

```bash
java -version
```

You should see output similar to:
```
java version "17.0.x"
```

### Verify Maven Installation

```bash
mvn -version
```

## Clone the Repository

```bash
git clone https://github.com/vortexbird/control-cambio-back.git
cd control-cambio-back
```

## Database Setup

### Create PostgreSQL Database

1. Connect to PostgreSQL:
```bash
psql -U postgres
```

2. Create the database:
```sql
CREATE DATABASE control_cambio;
CREATE USER cc_user WITH ENCRYPTED PASSWORD 'your_password';
GRANT ALL PRIVILEGES ON DATABASE control_cambio TO cc_user;
```

### Run Database Migrations

The application will automatically create the necessary tables on first run using JPA/Hibernate.

## Install Dependencies

Install all project dependencies using Maven:

```bash
mvn clean install
```

This will:
- Download all required dependencies
- Compile the source code
- Run unit tests (if configured)
- Package the application

<Note>
  If you want to skip tests during installation, use: `mvn clean install -DskipTests`
</Note>

## Configuration

Before running the application, you need to configure the database connection and other settings.

See the [Configuration Guide](/getting-started/configuration) for detailed instructions.

## Build the Application

Build the project for your target environment:

### Development Build

```bash
mvn clean package -Pdev
```

### Production Build

```bash
mvn clean package -Pprod
```

The built JAR file will be located in the `target/` directory.

## Docker Installation (Optional)

### Build Docker Image

```bash
docker build -t control-cambio:latest -f Dockerfile .
```

### Run with Docker Compose

Create a `docker-compose.yml` file:

```yaml
version: '3.8'
services:
  postgres:
    image: postgres:14
    environment:
      POSTGRES_DB: control_cambio
      POSTGRES_USER: cc_user
      POSTGRES_PASSWORD: your_password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  control-cambio:
    image: control-cambio:latest
    ports:
      - "8080:8080"
    environment:
      SPRING_PROFILES_ACTIVE: prod
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/control_cambio
      SPRING_DATASOURCE_USERNAME: cc_user
      SPRING_DATASOURCE_PASSWORD: your_password
    depends_on:
      - postgres

volumes:
  postgres_data:
```

Run with:
```bash
docker-compose up -d
```

## Next Steps

<Card title="Configuration" icon="gear" href="/getting-started/configuration">
  Configure your application settings
</Card>

<Card title="Quick Start" icon="rocket" href="/getting-started/quick-start">
  Run your first application instance
</Card>
