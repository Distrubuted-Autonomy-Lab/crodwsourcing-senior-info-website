# Development Guide – CareBot Crowdsourcing Platform

## 1. Introduction

This document serves as the Development Guide for the CareBot Crowdsourcing Platform. It is intended for developers who need to understand, set up, run, maintain, and contribute to the project. Unlike a user guide, which focuses on how end users interact with the application, this development guide focuses on the technical side of the system.

The CareBot Crowdsourcing Platform is a web-based application designed to help caregivers, family members, and social workers locate useful local resources for seniors. The system supports crowdsourced resource collection and management, and it is designed to make resource discovery easier by combining structured data, search, and recommendation functionality. Because the project deals with location-aware and service-based information, the system also uses geospatial database support through PostGIS. In addition, the project is structured for containerized deployment using Docker, making it easier to run across multiple environments.

This guide explains:

- what the project does,
- how the codebase is organized,
- what tools and frameworks are used,
- how to set up the project locally,
- how to run and test the application,
- common issues developers may face,
- and how new contributors should work within the repository.

The goal of this document is to make onboarding easier for any future team member. A new developer should be able to read this guide and understand how to get the project running without needing to rely entirely on verbal instructions from the original team.

## 2. Project Purpose and Scope

The CareBot platform was developed to address the difficulty many people face when trying to locate trustworthy senior support resources. In many communities, especially underserved or rural communities, important information about healthcare assistance, social support, housing resources, transportation, and legal or financial aid may be scattered across many different organizations. This creates a barrier for caregivers and family members who may already be under stress.

The system addresses this problem by providing a centralized platform where resource information can be submitted, stored, managed, and searched. Rather than depending only on a static list of services, the project uses a crowdsourcing model so that new resources can be added and updated over time. This allows the platform to grow as more information becomes available.

From a software perspective, the project includes several important ideas:

- a web application frontend that allows interaction with the system,
- a backend application layer that processes requests and manages business logic,
- a database layer for storing resource and user-related data,
- a recommendation or search component that helps return relevant results,
- and an infrastructure layer that supports deployment and scaling.

Because the system is intended to be maintainable and deployable beyond a classroom demonstration, the project uses development practices and technologies that are common in real-world software systems.

## 3. Intended Audience

This guide is written for:

- current team members working on the project,
- future student developers who may inherit the project,
- instructors or evaluators who want to understand the system from a technical perspective,
- and contributors who need a quick but thorough explanation of how the system is built.

A reader is not expected to already know the internal structure of the project, but they should have basic familiarity with software development concepts such as Git, Python, command line usage, and Docker.

## 4. High-Level System Overview

At a high level, the CareBot Crowdsourcing Platform consists of several major parts working together:

### Frontend / Web Interface

This is the part of the system users interact with in the browser. It is responsible for displaying pages, forms, and results.

### Backend Application

The backend handles routing, business logic, database operations, request processing, and integration with other services. In this project, Django serves as the core backend framework.

### Database

The database stores system data such as submitted resources, user data, and location-aware information. PostgreSQL is used as the primary relational database, and PostGIS extends it with spatial capabilities.

### Recommendation / Search Logic

A recommendation component helps match user queries to relevant resources. Depending on the implementation, this may involve embeddings, similarity matching, filtering, or ranking logic.

### Background / Messaging Services

RabbitMQ support is included for message passing or asynchronous task handling.

### Web Server / Reverse Proxy

Nginx is used to support request routing and static file handling in deployment contexts.

### Containerized Environment

Docker and Docker Compose make it possible to run the different services together in a reproducible way across different machines.

This separation is useful because each major part of the system has a defined responsibility. It also improves maintainability, since changes to one part of the project can often be made without rewriting the entire system.

## 5. Technology Stack

The project uses a combination of frameworks, services, and developer tools. Understanding why each technology is used helps future developers know where to make changes and how the pieces fit together.

### 5.1 Backend Framework — Django

Django is the main web framework used for the backend. It provides URL routing, models, views, templates, admin functionality, and database interaction. Django was a strong choice for this project because it allows rapid development while still supporting scalable and maintainable architecture.

### 5.2 Programming Language — Python

Python is the primary language used throughout the backend. It is used because of its readability, strong ecosystem, and compatibility with Django and AI-related tools.

### 5.3 Database — PostgreSQL and PostGIS

**PostgreSQL** is the main relational database used by the system. It is responsible for storing persistent application data.

**PostGIS** adds geospatial support to PostgreSQL. This is important for resource systems where proximity, region, or location-based search may matter.

### 5.4 Containerization and Infrastructure

**Docker** packages services into containers so the project can run consistently on different machines.

**Docker Compose** makes it easier to start multiple related services together, such as the application server, database, RabbitMQ, and nginx.

### 5.5 Messaging / Background Services — RabbitMQ

RabbitMQ is included to support asynchronous communication or task processing. This is useful in systems where some work should not block user requests.

### 5.6 Web Serving — Nginx

Nginx is used as a web server and reverse proxy. It can manage traffic, serve static files, and forward requests to application containers.

### 5.7 Version Control — Git / GitHub

Git is used for version control. GitHub is used to host the repository and support collaboration among team members.

## 6. Repository and Project Structure

A developer should understand the directory layout before making changes. While the exact structure may change over time, the repository contains several major folders and configuration files that indicate how the project is organized.

### 6.1 Root-Level Structure

At the root of the project, there are directories and files related to the application, deployment, support services, and data setup. Important folders include the following:

| Folder | Purpose |
|--------|---------|
| `app/` | Main application code and service-related files |
| `carebot/` | Main Django project configuration and application logic |
| `chat/` | Chat or conversation-based interactions, if enabled |
| `recommendation/` | Recommendation, matching, or AI-powered search behavior |
| `nginx/` | Configuration files for the nginx service |
| `rmq/` | RabbitMQ configuration or support |
| `scripts/` | Helper scripts for setup, automation, or maintenance |
| `imports/` | Imported data files (CSV, etc.) used to populate the system |
| `images/` | Project diagrams, architecture images, or documentation assets |
| `pgisvector/` | PostGIS vector or spatial database configuration support |

### 6.2 Important Configuration Files

Several files are especially important for developers:

| File | Role |
|------|------|
| `manage.py` | Standard Django entry point for migrations, runserver, testing, and admin commands |
| `requirements.txt` | Python dependencies |
| `Dockerfile` | How the application Docker image is built |
| `docker-compose.yml` | Default multi-container configuration |
| `docker-compose.local.yml` | Local development container setup |
| `docker-compose.gcloud.yml` | Cloud deployment configuration |
| `.env-local-example` / `.env-local.example` | Example environment template for local setup |
| `.env-local` | Developer-specific environment file (create from the example; not committed with secrets) |
| `database_dump.sql` / `carebot.sql` | SQL dumps for initialization or restore |

### 6.3 Why Structure Matters

The project structure reflects separation of concerns. A developer working on backend logic will spend most of their time in the Django-related folders. A developer working on deployment or configuration will spend more time in Docker, nginx, and environment configuration files. Someone working on search or recommendation behavior will likely spend more time in the `recommendation/` directory.

This separation improves readability and makes it easier to divide work among team members.

## 7. Development Environment Setup

This section explains how to set up the project on a local machine. These steps are written with the assumption that a developer is starting from scratch.

### 7.1 Prerequisites

Before attempting setup, make sure the following tools are installed:

- **Git** — clone and manage the repository
- **Docker** — run the application and supporting services in containers
- **Docker Compose** — bring up multi-container configurations
- **Python 3.x** (optional but useful) — run local commands outside Docker or inspect dependencies
- **A code editor or IDE** — VS Code, PyCharm, or another editor with Python support

### 7.2 Clone the Repository

Clone the application repository from GitHub, then open the directory that contains `manage.py` and the Compose files (often `root/` in this project):

```bash
git clone https://github.com/Distrubuted-Autonomy-Lab/carebot-deployable-crowdsourcing.git
cd carebot-deployable-crowdsourcing/root
```

### 7.3 Review Project Files

After cloning, confirm that expected directories and configuration files are present: `app/`, `carebot/`, `chat/`, `recommendation/`, `nginx/`, `rmq/`, `scripts/`, `imports/`, `images/`, `pgisvector/`, `manage.py`, `requirements.txt`, `Dockerfile`, `docker-compose.yml`, `docker-compose.local.yml`, `docker-compose.gcloud.yml`, and an env example file such as `.env-local-example` or `.env-local.example`.

### 7.4 Create the Local Environment File

Copy the example environment file and edit it for your machine:

```bash
cp .env-local-example .env-local
# or: cp .env-local.example .env-local
```

The `.env-local` file stores environment-specific values (database host/port, credentials, secret keys, debug flags, service URLs, ports). Many startup issues come from a missing variable, a typo, or a value pointing at the wrong service.

### 7.5 Build and Start the Application

From the directory that contains the Compose files, start the stack. This project typically uses **both** the base and local override files:

```bash
docker compose -f docker-compose.yml -f docker-compose.local.yml up --build
```

Some setups document the shorter form:

```bash
docker-compose -f docker-compose.local.yml up --build
```

Use whichever matches your checked-in `docker-compose` files. The `--build` flag ensures recent Dockerfile and dependency changes are included.

### 7.6 Confirm Services Are Running

```bash
docker ps
```

Verify the application, database, and any required services (RabbitMQ, nginx) are running and not exiting immediately. If a container stops, inspect Compose logs for errors.

### 7.7 Access the Application

Open the app in a browser. For this repository’s local setup, the app is usually at:

**http://localhost:8000/**

If the port differs, check `docker-compose.local.yml` and your `.env-local` values.

### 7.8 Stop the Development Environment

```bash
docker compose -f docker-compose.yml -f docker-compose.local.yml down
```

(or the equivalent `docker-compose ... down` for your compose invocation.)

## 8. Database Setup and Migrations

Because the project relies on PostgreSQL and PostGIS, developers need to understand how the database schema is created and updated.

### 8.1 Running Migrations

Apply Django migrations inside the application container (replace `<app_container_name>` with your running web/app service name, often `web`):

```bash
docker exec -it <app_container_name> python manage.py migrate
```

### 8.2 Loading Initial Data

If the system requires preloaded data, SQL dumps such as `database_dump.sql` or `carebot.sql` may be imported into PostgreSQL. The exact import method depends on how the database container is configured.

### 8.3 Importance of PostGIS

PostGIS supports storing coordinates, region search, distance filtering, and location-aware recommendations. If geographic queries fail unexpectedly, verify PostGIS extension and database setup.

## 9. Running the Application in Development

A successful local run usually means: containers are up, the database is reachable, migrations are applied, the app loads in the browser, and main flows work without major errors.

Monitor logs while developing:

```bash
docker compose -f docker-compose.yml -f docker-compose.local.yml logs
docker compose -f docker-compose.yml -f docker-compose.local.yml logs -f
```

## 10. Key Functional Components

### 10.1 Crowdsourcing Functionality

Submission forms, validation, storage, updates, and moderation workflows allow the catalog to grow over time.

### 10.2 Search and Recommendation System

Semantic or lexical matching, ranking, filtering, and AI-assisted strategies may live under `recommendation/` and related modules.

### 10.3 Chat or Conversational Support

The `chat/` area may connect user input to recommendation logic and formatted responses.

### 10.4 Geospatial Resource Handling

Understand how location is stored, queried, and shown in the UI when working on “near me” or regional features.

### 10.5 Background Processing and Messaging

RabbitMQ may handle asynchronous tasks; ensure the broker and workers are running and connected when developing those paths.

## 11. Developer Workflow

### 11.1 Pull the Latest Changes

```bash
git pull origin main
```

(Use your team’s default branch name if it differs.)

### 11.2 Create a Separate Branch

Use feature branches instead of committing directly to `main`, for example:

- `feature/improve-resource-search`
- `bugfix/fix-db-connection`
- `docs/update-development-guide`

### 11.3 Make and Verify Changes

Run locally, exercise affected flows, and watch logs for regressions.

### 11.4 Commit Clearly

Use messages that state what changed and why.

### 11.5 Push and Open a Pull Request

```bash
git push origin <branch-name>
```

Include a summary, how you tested, and any reviewer context.

## 12. Code Quality and Contribution Expectations

- Write readable code with clear names and focused functions.
- Follow existing patterns before introducing new ones.
- Keep branches and PRs scoped to one concern.
- Update documentation when setup, structure, or major workflows change.
- Use code review before merging when possible.

## 13. Summary

The CareBot Crowdsourcing Platform combines a Django backend, PostgreSQL/PostGIS, recommendation features, Docker-based infrastructure, and clear separation of concerns. For onboarding: clone the repo, review structure, create `.env-local`, start Compose, verify containers, run migrations as needed, open the app in the browser, and use logs to debug issues.

This guide is intended to reduce onboarding friction, improve maintainability, and give future developers a clear path to contribute.

---

**Also on this site:** [Final project](finalproject.html) (course rubric hub) · [Development guide (HTML)](development-guide.html) · [Quick development environment setup](development-environment.html) · [Run & deploy](deploy-run.html)
