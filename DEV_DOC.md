# Developer Documentation

This document describes the internal architecture and technical implementation of the Inception infrastructure.

---

# Infrastructure Overview

The project deploys a containerized WordPress stack using Docker Compose.

The infrastructure consists of three services:

- NGINX
- WordPress (PHP-FPM)
- MariaDB

Each service runs inside its own container and communicates through a Docker network.

---

# System Architecture

```
Browser
│
│ HTTPS (TLS 1.2 / TLS 1.3)
▼
NGINX
│
│ FastCGI
▼
WordPress (PHP-FPM)
│
│ MySQL protocol
▼
MariaDB
```
---

# Container Responsibilities

## NGINX

The NGINX container acts as the entrypoint to the infrastructure.

Responsibilities:

- Accept HTTPS connections
- Handle TLS encryption
- Serve static files
- Forward PHP requests to the WordPress container

NGINX communicates with WordPress using FastCGI.

Example configuration:
```
location ~ .php$ {
fastcgi_pass wordpress:9000;
}
```
The container exposes port `443` to the host system.

---

## WordPress

The WordPress container runs:

- WordPress
- PHP-FPM

PHP-FPM executes PHP scripts requested by NGINX.

The container connects to the MariaDB database using environment variables.

WordPress initialization is performed using **WP-CLI**, which:

1. Downloads WordPress
2. Creates `wp-config.php`
3. Installs WordPress
4. Creates admin and user accounts

---

## MariaDB

MariaDB provides the relational database used by WordPress.

Responsibilities:

- store posts
- store user accounts
- store WordPress configuration
- persist website data

The database container initializes the database and creates the WordPress user automatically on startup.

---

# Docker Networking

All containers communicate using a **Docker bridge network**.

Example network definition:
```
networks:
inception:
driver: bridge
```

Because they share the same network, containers can communicate using **service names as hostnames**.

Examples:
```
wordpress → mariadb
nginx → wordpress
```

This avoids the need for manual IP configuration.

---

# Volumes and Data Persistence

Docker volumes are used to ensure data persists across container restarts.

Two volumes are defined:
```
/home/<login>/data/mariadb
/home/<login>/data/wordpress
```

hese volumes store:

MariaDB volume

- database files
- tables
- user accounts

WordPress volume

- WordPress core files
- plugins
- themes
- uploaded media

If containers are destroyed, the data remains on the host.

---

# Environment Variables

Configuration values are stored in the `.env` file.

Examples:

```
MYSQL_DATABASE
MYSQL_USER
MYSQL_PASSWORD
WORDPRESS_ADMIN
WORDPRESS_ADMIN_PASSWORD
```

These variables are injected into containers through Docker Compose.

This allows configuration to be modified without rebuilding images.

Sensitive data is therefore not hardcoded into Dockerfiles.

---

# Service Startup Order

The WordPress container depends on the MariaDB container.

Docker Compose uses a health check to ensure MariaDB is ready before WordPress starts.

Example:
```
depends_on:
mariadb:
condition: service_healthy
```

This prevents WordPress from attempting a database connection before MariaDB is ready.

---

# TLS and HTTPS

NGINX uses a self-signed certificate generated with OpenSSL.

The certificate is generated during container startup.

Example command:
```
openssl req -x509 -nodes -days 365
-newkey rsa:2048
-keyout /etc/nginx/ssl/inception.key
-out /etc/nginx/ssl/inception.crt
```
The NGINX configuration restricts TLS versions to:
```
TLSv1.2
TLSv1.3
```

This ensures secure communication between the browser and the server.

---

# Container Restart Policy

All containers use the following restart policy:
```
restart: always
```

This ensures services automatically restart if a container crashes.

---

# Security Considerations

Security practices implemented:

- HTTPS-only access
- TLS 1.2 / TLS 1.3 encryption
- database credentials stored in environment variables
- isolated containers
- internal Docker networking

NGINX is the only container exposed to the outside network.

---

# Development Workflow

Typical workflow for developers:

Build and start infrastructure:
```
make
```

Stop containers:
```
make down
```

Reset project:
```
make fclean
```

---

# Summary

The Inception infrastructure demonstrates how to deploy a containerized web application using:

- container isolation
- service orchestration
- secure networking
- persistent storage

This architecture mirrors a simplified production WordPress deployment.

