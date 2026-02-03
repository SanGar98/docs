---
title: 'Deployment Guide'
description: 'Deploy Control de Cambios to production'
---

# Deployment Guide

Deploy Control de Cambios to production environments using Docker or traditional deployment methods.

## Prerequisites

- PostgreSQL database (production instance)
- Java 17+ runtime
- Docker (for containerized deployment)
- HTTPS/SSL certificates (for production)
- Reverse proxy (Nginx/Apache) - recommended

## Production Build

### Build JAR File

```bash
mvn clean package -Pprod -DskipTests
```

This creates: `target/control-cambio-prod.jar`

### Build Docker Image

```bash
docker build -t control-cambio:1.0.0 -f Dockerfile.prod .
```

## Docker Deployment

### Docker Compose (Recommended)

Create `docker-compose.prod.yml`:

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:14-alpine
    container_name: cc-postgres
    environment:
      POSTGRES_DB: control_cambio
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - cc-network
    restart: unless-stopped

  control-cambio:
    image: control-cambio:1.0.0
    container_name: cc-backend
    environment:
      SPRING_PROFILES_ACTIVE: prod
      DATABASE_URL: jdbc:postgresql://postgres:5432/control_cambio
      DATABASE_USERNAME: ${DB_USER}
      DATABASE_PASSWORD: ${DB_PASSWORD}
      JWT_SECRET: ${JWT_SECRET}
      SSO_BASE_URL: ${SSO_BASE_URL}
      SSO_CLIENT_ID: ${SSO_CLIENT_ID}
      SSO_CLIENT_SECRET: ${SSO_CLIENT_SECRET}
      ALLOWED_ORIGINS: ${ALLOWED_ORIGINS}
    ports:
      - "8080:8080"
    depends_on:
      - postgres
    networks:
      - cc-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/actuator/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  nginx:
    image: nginx:alpine
    container_name: cc-nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - control-cambio
    networks:
      - cc-network
    restart: unless-stopped

volumes:
  postgres_data:

networks:
  cc-network:
    driver: bridge
```

### Environment Variables

Create `.env` file:

```bash
DB_USER=cc_user
DB_PASSWORD=STRONG_PASSWORD_HERE
JWT_SECRET=YOUR_SECRET_KEY_MINIMUM_256_BITS
SSO_BASE_URL=https://sso.vortexbird.com
SSO_CLIENT_ID=control-cambio
SSO_CLIENT_SECRET=YOUR_SSO_SECRET
ALLOWED_ORIGINS=https://app.vortexbird.com
```

<Warning>
Never commit `.env` files to version control. Add to `.gitignore`.
</Warning>

### Start Services

```bash
docker-compose -f docker-compose.prod.yml up -d
```

### Check Logs

```bash
docker-compose -f docker-compose.prod.yml logs -f control-cambio
```

## Traditional Deployment

### 1. Prepare Server

```bash
# Install Java 17
sudo apt update
sudo apt install openjdk-17-jre

# Create application user
sudo useradd -r -s /bin/false ccuser
sudo mkdir -p /opt/control-cambio
sudo chown ccuser:ccuser /opt/control-cambio
```

### 2. Deploy JAR

```bash
# Copy JAR to server
scp target/control-cambio-prod.jar user@server:/opt/control-cambio/

# Set permissions
sudo chown ccuser:ccuser /opt/control-cambio/control-cambio-prod.jar
```

### 3. Create Systemd Service

Create `/etc/systemd/system/control-cambio.service`:

```ini
[Unit]
Description=Control de Cambios Backend
After=network.target postgresql.service

[Service]
Type=simple
User=ccuser
WorkingDirectory=/opt/control-cambio
ExecStart=/usr/bin/java -jar /opt/control-cambio/control-cambio-prod.jar
SuccessExitStatus=143
Restart=on-failure
RestartSec=10

# Environment
Environment="SPRING_PROFILES_ACTIVE=prod"
Environment="DATABASE_URL=jdbc:postgresql://localhost:5432/control_cambio"
Environment="DATABASE_USERNAME=cc_user"
Environment="DATABASE_PASSWORD=PASSWORD_HERE"

# Logging
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

### 4. Start Service

```bash
sudo systemctl daemon-reload
sudo systemctl enable control-cambio
sudo systemctl start control-cambio
sudo systemctl status control-cambio
```

## Nginx Configuration

Create `/etc/nginx/sites-available/control-cambio`:

```nginx
upstream backend {
    server localhost:8080;
}

server {
    listen 80;
    server_name api.vortexbird.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name api.vortexbird.com;

    ssl_certificate /etc/nginx/ssl/certificate.crt;
    ssl_certificate_key /etc/nginx/ssl/private.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    client_max_body_size 10M;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }

    location /actuator/health {
        proxy_pass http://backend/actuator/health;
        access_log off;
    }
}
```

Enable site:

```bash
sudo ln -s /etc/nginx/sites-available/control-cambio /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## Database Migration

### Production Database Setup

```sql
-- Create database
CREATE DATABASE control_cambio
    WITH ENCODING 'UTF8'
    LC_COLLATE = 'en_US.UTF-8'
    LC_CTYPE = 'en_US.UTF-8'
    TEMPLATE template0;

-- Create user
CREATE USER cc_user WITH ENCRYPTED PASSWORD 'strong_password';
GRANT ALL PRIVILEGES ON DATABASE control_cambio TO cc_user;

-- Connect to database
\c control_cambio

-- Grant schema permissions
GRANT ALL ON SCHEMA public TO cc_user;
```

### Initial Data

```sql
-- Insert default statuses
INSERT INTO cc_estado (esta_nombre, esta_descripcion, esta_color, esta_activo) VALUES
('PENDIENTE', 'Pending review', '#FFA500', true),
('APROBADO', 'Approved', '#008000', true),
('EN_PROGRESO', 'In progress', '#0000FF', true),
('COMPLETADO', 'Completed', '#006400', true),
('RECHAZADO', 'Rejected', '#FF0000', true),
('CANCELADO', 'Cancelled', '#808080', true);

-- Create admin user
INSERT INTO cc_user_application (usap_username, usap_password, usap_email, usap_nombre, usap_activo, usap_roles)
VALUES ('admin', '$2a$12$ENCRYPTED_PASSWORD', 'admin@vortexbird.com', 'Administrator', true, 'ROLE_ADMIN');
```

## Monitoring

### Health Check

```bash
curl https://api.vortexbird.com/actuator/health
```

### Application Metrics

Access Spring Boot Actuator endpoints:

- `/actuator/health` - Health status
- `/actuator/metrics` - Application metrics
- `/actuator/info` - Application info

### Log Management

```bash
# View logs
sudo journalctl -u control-cambio -f

# Check recent errors
sudo journalctl -u control-cambio --since "1 hour ago" | grep ERROR
```

## Backup Strategy

### Database Backup

```bash
# Daily backup script
#!/bin/bash
BACKUP_DIR="/backup/cc"
DATE=$(date +%Y%m%d_%H%M%S)

pg_dump -U cc_user control_cambio > $BACKUP_DIR/cc_backup_$DATE.sql
gzip $BACKUP_DIR/cc_backup_$DATE.sql

# Keep last 30 days
find $BACKUP_DIR -name "cc_backup_*.sql.gz" -mtime +30 -delete
```

Schedule with cron:

```bash
0 2 * * * /opt/scripts/backup_cc.sh
```

## Scaling

### Horizontal Scaling

Run multiple instances behind a load balancer:

```yaml
# docker-compose.scale.yml
services:
  control-cambio:
    deploy:
      replicas: 3
```

```bash
docker-compose -f docker-compose.scale.yml up -d --scale control-cambio=3
```

### Load Balancer (Nginx)

```nginx
upstream backend {
    least_conn;
    server backend1:8080;
    server backend2:8080;
    server backend3:8080;
}
```

## Troubleshooting

### Application Won't Start

1. Check logs: `sudo journalctl -u control-cambio`
2. Verify database connection
3. Check environment variables
4. Ensure correct Java version

### High Memory Usage

Adjust JVM options in systemd service:

```ini
ExecStart=/usr/bin/java -Xms512m -Xmx2g -jar control-cambio-prod.jar
```

### Database Connection Issues

1. Verify PostgreSQL is running
2. Check firewall rules
3. Verify connection credentials
4. Check `pg_hba.conf` for access rules
