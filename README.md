# Inception
A containerized WordPress infrastructure built with Docker Compose as part of the **42 School Inception project.**

The project demonstrates how to design a small production-like infrastructure using containers, persistent storage, networking and secure
HTTPS communication.

---
# Project Overview
This project deploys a WordPress website using three Docker containers:
- NGINX – reverse proxy and HTTPS entrypoint
- WordPress + PHP-FPM – application server
- MariaDB – database server

Each service runs inside its own container and communicates through a **Docker network.**

The infrastructure is orchestrated using **Docker Compose** and includes *persistent volumes* so that data survives container restarts.

## Architecture
```
  Browser
     │
     │ HTTPS (TLS 1.2 / TLS 1.3)
     ▼
  NGINX container
     │
     │ FastCGI
     ▼
  WordPress container (PHP-FPM)
     │
     │ SQL
     ▼
  MariaDB container
```

## Components
### NGINX
- Only entry point to the infrastructure
- Handles HTTPS connections
- Forwards PHP requests to WordPress via FastCGI
### WordPress
- Runs with PHP-FPM
- Serves the WordPress application
- Communicates with MariaDB for content storage
#### MariaDB
- Stores WordPress data
- Uses a persistent Docker volume

## Technologies Used
- Docker
- Docker Compose
- NGINX
- WordPress
- PHP-FPM
- MariaDB
- OpenSSL (for TLS certificates)
- Alpine Linux

## Project structure
```
inception/
│
├── Makefile
├── srcs/
│   ├── docker-compose.yml
│   ├── .env
│   │
│   └── requirements/
│       ├── nginx/
│       │   ├── Dockerfile
│       │   ├── conf/
│       │   └── tools/
│       │
│       ├── wordpress/
│       │   ├── Dockerfile
│       │   ├── conf/
│       │   └── tools/
│       │
│       └── mariadb/
│           ├── Dockerfile
│           ├── conf/
│           └── tools/
```

## Installation & Usage
### 1.Clone the repository
```
git clone <repo>
cd inception
```
### 2.Create/Configure enviroment variables
```
srcs/.env
```
**Example Variables.**
```
DOMAIN_NAME=rhvidste.42.fr

MYSQL_DATABASE=wordpress_db
MYSQL_USER=rhvidste
MYSQL_PASSWORD=*****

WORDPRESS_ADMIN=ross
WORDPRESS_ADMIN_PASSWORD=*****
WORDPRESS_ADMIN_EMAIL=admin@rhvidste.42.fr
```
### 3.Build and start containers
```
make
```
This will:
- create persistent directories
- build docker images
- start containers in detached mode



## Section
  This is a section
  This project deploys a WordPress infrastructure using Docker containers.
  - one
  - two
  - three

  1. Build containers
  2. Start services
  3. Open website

  ```yaml
  services:
    nginx:
  ```
---

# 5. Inline code

Use **single backticks**.

```markdown
Run `make` to start the containers.
```

### Subsection
  This is a subsection
  6. File structure formatting
  ```
    inception/
    ├── Makefile
    ├── srcs/
    │   ├── docker-compose.yml
    │   └── requirements/
  ```
#### Smaller subsection
  This is a smaller subsection
  [WordPress](https://wordpress.org)
  This project uses **Docker Compose**.


