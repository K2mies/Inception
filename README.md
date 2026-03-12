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
### 1. Clone the repository
```
git clone <repo>
cd inception
```
### 2. Create/Configure enviroment variables
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
### 3. Build and start containers
```
make
```
This will:
- create persistent directories
- build docker images
- start containers in detached mode

### 4. Access the website
Open in your browser:
```
https://rhvidste.42.fr
```
Make sure /etc/hosts contains:
```
127.0.0.1 rhvidste.42.fr
```

## Useful Commands
Start containers
```bash
make
```
Stop containers
```bash
make down
```
restart containers
```bash
make up
```
Clean containers and volumes
```bash
make clean 
```
Clean everything
```bash
make fclean
```
full reset
```bash
make re
```

## Docker Concepts
### Containers
Containers package applications and their dependencies into isolated environments that run consistently across systems.

Each service in this project runs in its own container.

## Docker Compose
Docker Compose allows multiple containers to be defined and run together using a single configuration file (docker-compose.yml).

it manages:
- container creation
- networking
- volumes
- dependencies

## Docker Networks
Containers communicate through a custom bridge network.

This allows services to connect using container names:
```
wordpress -> mariadb
nginx -> wordpress
```

## Volumes
Volumes store persistent data outside the container filesystem.

This project uses two volumes:
```
/home/rhvidste/data/mariadb
/home/rhvidste/data/wordpress
```
This ensures that data remains even if containers are deleted.

## Bind Mounts
Bind mounts map directories from the host machine into containers.

They are used here to persist database and WordPress files.

## Enviroment Variables
Sensitive configuration such as database credentials are stored in a .env file rather than hardcoded into Dockerfiles.

This allows the configuration to be changed without rebuilding images.

## Docker vs Virtual Machines
Virtual Machines run a full operating system for each instance, which consumes more resources.

Docker containers share the host kernel and are therefore:
- lighter
- faster to start
- more efficient

## Security
The infrastructure uses HTTPS with TLS certificates generated using OpenSSL.

NGINX only allows:
```
TLSv1.2
TLSv1.3
```
This ensures encrypted communication between clients and the server.
