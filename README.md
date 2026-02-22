# Docker

> Docker: PODA — Package Once, Deploy Anywhere

A collection of Docker examples covering Dockerfiles, Docker Compose, multi-container applications, and Docker Swarm mode.

---

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Examples](#examples)
  - [Hello Java](#hello-java)
  - [Hello Web](#hello-web)
  - [Docker Compose Examples](#docker-compose-examples)
  - [Swarm Mode](#swarm-mode)
- [Cheat Sheets](#cheat-sheets)
- [Getting Started](#getting-started)

---

## Overview

This project demonstrates Docker fundamentals through practical examples — containerizing a Java application, deploying a Java EE web app on WildFly, orchestrating multi-service stacks with Docker Compose, and setting up a production-like Docker Swarm cluster.

---

## Project Structure

```
docker/
├── hellojava/
│   ├── Dockerfile                        # Java app container (OpenJDK Alpine)
│   └── myapp/                            # Maven Java project
│       └── src/main/java/App.java        # Simple Java application
├── helloweb/
│   ├── Dockerfile                        # WildFly container with WAR deployment
│   └── webapp.war                        # Java EE web application artifact
├── dockercompose/
│   ├── helloweb/
│   │   ├── docker-compose.yml            # WildFly app via Compose
│   │   ├── docker-compose.override.yml   # Override configuration
│   │   ├── docker-compose.extends.yml    # Extends pattern
│   │   ├── configuration.yml             # App configuration
│   │   └── deployments/webapp.war        # Deployed WAR file
│   └── travel/
│       └── docker-compose.yml            # Couchbase + Java EE travel app
├── swarmMode/
│   ├── swarm-cluster.sh                  # Initializes 3-manager / 3-worker swarm
│   └── swarm-machines.sh                 # Creates docker-machine VMs
├── DockerForMacCheatSheet                # macOS Docker commands reference
├── DockerComposeCheatSheet               # Docker Compose commands reference
├── DockerMonitoringCheatSheet            # Docker monitoring commands reference
└── SwarmModeCheatSheet                   # Docker Swarm commands reference
```

---

## Examples

### Hello Java

Containerizes a simple Java application using an OpenJDK Alpine image.

**Dockerfile:**
```dockerfile
FROM openjdk:jdk-alpine
COPY myapp/target/myapp.jar /deployments/
CMD java -jar /deployments/myapp.jar
```

**Build and run:**
```bash
cd hellojava
mvn package
docker build -t hellojava .
docker run hellojava
```

---

### Hello Web

Deploys a Java EE WAR file on a WildFly application server.

**Dockerfile:**
```dockerfile
FROM jboss/wildfly
COPY webapp.war /opt/jboss/wildfly/standalone/deployments/webapp.war
```

**Build and run:**
```bash
cd helloweb
docker build -t helloweb .
docker run -p 8080:8080 helloweb
```

Access: `http://localhost:8080/webapp`

---

### Docker Compose Examples

#### WildFly Web App (`dockercompose/helloweb/`)
Mounts a local WAR file into WildFly via a volume:
```yaml
services:
  web:
    image: jboss/wildfly
    volumes:
      - ./deployments:/opt/jboss/wildfly/standalone/deployments
    ports:
      - 8080:8080
```

```bash
cd dockercompose/helloweb
docker-compose up
```

#### Travel App (`dockercompose/travel/`)
Multi-container stack with a Java EE app and Couchbase database:

```yaml
services:
  web:
    image: arungupta/couchbase-javaee:travel
    environment:
      - COUCHBASE_URI=db
    ports:
      - 8080:8080
      - 9990:9990
    depends_on:
      - db
  db:
    image: arungupta/couchbase:travel
    ports:
      - 8091:8091
```

```bash
cd dockercompose/travel
docker-compose up
```

---

### Swarm Mode

Sets up a production-like Docker Swarm cluster with 3 managers and 3 workers using `docker-machine`.

```bash
# Create VMs and initialize swarm
bash swarmMode/swarm-cluster.sh

# Verify cluster
docker node ls
```

The cluster topology:
- `manager1` — Swarm leader (initializes the swarm)
- `manager2`, `manager3` — Additional managers (join as managers)
- `worker1`, `worker2`, `worker3` — Worker nodes

---

## Cheat Sheets

| File | Contents |
|------|---------|
| `DockerForMacCheatSheet` | Common Docker commands for macOS |
| `DockerComposeCheatSheet` | Docker Compose up/down/build/logs commands |
| `DockerMonitoringCheatSheet` | `docker stats`, `docker inspect`, monitoring |
| `SwarmModeCheatSheet` | Swarm init, service deploy, scaling |

---

## Getting Started

**Prerequisites:** Docker Desktop installed and running.

```bash
# Verify Docker installation
docker --version
docker-compose --version

# Pull and run a quick test
docker run hello-world
```
