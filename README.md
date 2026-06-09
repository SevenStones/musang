<p align="center">
  <img src="docs/images/musang-banner.png" alt="Musang - Oracle Database Security Scanner" width="600">
</p>

# Musang

**Musang** is an Oracle Database Security Scanner designed to assess Oracle database configurations, identify security weaknesses, review authentication controls, and perform a variety of security assessment checks against Oracle database instances.

Musang was originally developed by Seven Stones Information Security (UK) and has been modernised to run entirely within Docker containers.

The current build supports testing of Oracle Database versions 10g, 11g, and 12c. Future builds will support more recent Oracle Database versions.

---

## Features

- Oracle database security assessment
- Oracle authentication review
- Privilege analysis
- Oracle configuration auditing
- Security baseline checking
- Web-based interface
- Celery-backed asynchronous task processing
- RabbitMQ message broker
- MySQL backend database
- Docker Compose deployment

---

## Architecture

The application consists of the following containers:

| Container | Purpose |
|------------|------------|
| orasec-web | Apache + Django application |
| orasec-celery | Background task processing |
| orasec-mysql | Application database |
| orasec-rabbitmq | Message queue |


---

## Platform Requirements

### Supported Platforms

Musang is designed to run on:

- Ubuntu
- Debian
- Kali Linux
- Linux Mint
- Fedora
- Rocky Linux
- AlmaLinux
- Red Hat Enterprise Linux
- SUSE Linux

provided Docker is installed.

### Hardware Requirements

Recommended:

- 2 CPU cores
- 4 GB RAM minimum
- 8 GB RAM recommended
- 10 GB free disk space

### CPU Architecture

Supported:

- x86_64 / amd64

Not currently supported:

- ARM64
- Raspberry Pi
- Apple Silicon (unless running x86 emulation)

---

## Prerequisites

### Docker

Install:

- Docker Engine
- Docker Compose v2

Verify:

```bash
docker --version
docker compose version

# Oracle Instant Client

Oracle Instant Client is required for Oracle database connectivity.

## Licensing Notice

Oracle Instant Client is distributed under Oracle licensing terms and cannot normally be redistributed within this repository.

The Docker build expects the Oracle Instant Client package to be supplied separately.

## Required Package

Obtain:

```
instantclient12.zip
```

or an equivalent Oracle Instant Client 12.1 package.

Place it in the project root:

```
musang/
├── Dockerfile
├── docker-compose.yml
├── instantclient12.zip
└── ...
```

## Oracle Download

Oracle Instant Client can be obtained from:

https://www.oracle.com/database/technologies/instant-client.html

An Oracle account may be required to access historical releases.

---

# Installation

## Clone Repository

```bash
git clone https://github.com/<organisation>/musang.git

cd musang
```

## Create Environment File

Copy:

```bash
cp .env.example .env
```

Edit `.env`:

```ini
MYSQL_HOST=mysql
MYSQL_PORT=3306

MYSQL_DATABASE=orasec
MYSQL_USER=orasec
MYSQL_PASSWORD=changeme

MYSQL_ROOT_PASSWORD=changeme

DJANGO_SETTINGS_MODULE=orasec.settings

SECRET_KEY=replace-with-random-secret

DJANGO_ADMIN_USER=admin
DJANGO_ADMIN_EMAIL=admin@example.com
DJANGO_ADMIN_PASSWORD=changeme

RABBITMQ_HOST=rabbitmq
RABBITMQ_PORT=5672
```

---

# Build

Build all containers:

```bash
docker compose build
```

For a clean rebuild:

```bash
docker compose build --no-cache
```

---

# Start

Start Musang:

```bash
docker compose up
```

Or run in the background:

```bash
docker compose up -d
```

The first startup will automatically:

- Create the MySQL database.
- Run Django migrations.
- Collect static files.
- Create the Django administrator account.
- Start Apache.
- Start the Celery worker.
- Start RabbitMQ.

---

# Accessing Musang

Browse to:

```
http://localhost:8080
```

or

```
http://<server-ip>:8080
```

Default administrator credentials are those specified in:

```
DJANGO_ADMIN_USER
DJANGO_ADMIN_PASSWORD
```

within `.env`.

---

# Verifying Containers

```bash
docker compose ps
```

Expected output:

```
orasec-web        Up
orasec-celery     Up
orasec-mysql      Up (healthy)
orasec-rabbitmq   Up
```

---

# Viewing Logs

All containers:

```bash
docker compose logs -f
```

Web:

```bash
docker compose logs -f web
```

Celery:

```bash
docker compose logs -f celery
```

MySQL:

```bash
docker compose logs -f mysql
```

RabbitMQ:

```bash
docker compose logs -f rabbitmq
```

Apache logs inside the container:

```bash
docker exec -it orasec-web bash

tail -f /var/log/apache2/error.log
```

---

# Stopping Musang

Stop the application:

```bash
docker compose down
```

---

# Removing All Data

**WARNING**

This removes the MySQL database and all persisted application state.

```bash
docker compose down -v
```

---

# Troubleshooting

## Oracle Client Errors

Verify that the Oracle shared libraries are installed correctly:

```bash
docker exec -it orasec-web bash

ldconfig -p | grep clntsh
```

## RabbitMQ Errors

Verify the Celery worker:

```bash
docker compose logs celery
```

Verify RabbitMQ:

```bash
docker compose logs rabbitmq
```

## Static File Problems

Perform a clean rebuild:

```bash
docker compose build --no-cache

docker compose up
```

---

# Security Notes

Before deploying into production:

- Change all default passwords.
- Generate a unique Django `SECRET_KEY`.
- Restrict access using firewall rules.
- Enable HTTPS using a reverse proxy.
- Review Oracle account privileges.
- Store secrets securely.

---

# Copyright

Copyright (c) Seven Stones Information Security (UK).

All rights reserved.

Refer to the project licence for full licensing details.

---

# Acknowledgements

This project makes use of:

- Django
- Apache HTTP Server
- Celery
- RabbitMQ
- MySQL
- Oracle Instant Client
- Docker
- Docker Compose
