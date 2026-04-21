# Development Guide – CareBot Crowdsourcing Platform

## 1. Introduction

This document serves as the **Development Guide** for the CareBot Crowdsourcing Platform. It is intended for developers who need to understand, set up, run, maintain, and contribute to the project. Unlike a user guide, which focuses on how end users interact with the application, this development guide focuses on the technical side of the system.

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

---

## 2. Project Purpose and Scope

The CareBot platform was developed to address the difficulty many people face when trying to locate trustworthy senior support resources. In many communities, especially underserved or rural communities, important information about healthcare assistance, social support, housing resources, transportation, and legal or financial aid may be scattered across many different organizations. This creates a barrier for caregivers and family members who may already be under stress.

The system addresses this problem by providing a centralized platform where resource information can be submitted, stored, managed, and searched. Rather than depending only on a static list of services, the project uses a crowdsourcing model so that new resources can be added and updated over time. This allows the platform to grow as more information becomes available.

From a software perspective, the project includes several important ideas:
- a **web application frontend** that allows interaction with the system,
- a **backend application layer** that processes requests and manages business logic,
- a **database layer** for storing resource and user-related data,
- a **recommendation or search component** that helps return relevant results,
- and an **infrastructure layer** that supports deployment and scaling.

Because the system is intended to be maintainable and deployable beyond a classroom demonstration, the project uses development practices and technologies that are common in real-world software systems.

---

## 3. Intended Audience

This guide is written for:
- current team members working on the project,
- future student developers who may inherit the project,
- instructors or evaluators who want to understand the system from a technical perspective,
- and contributors who need a quick but thorough explanation of how the system is built.

A reader is not expected to already know the internal structure of the project, but they should have basic familiarity with software development concepts such as Git, Python, command line usage, and Docker.

---

## 4. High-Level System Overview

At a high level, the CareBot Crowdsourcing Platform consists of several major parts working together:

1. **Frontend / Web Interface**  
   This is the part of the system users interact with in the browser. It is responsible for displaying pages, forms, and results.

2. **Backend Application**  
   The backend handles routing, business logic, database operations, request processing, and integration with other services. In this project, Django serves as the core backend framework.

3. **Database**  
   The database stores system data such as submitted resources, user data, and location-aware information. PostgreSQL is used as the primary relational database, and PostGIS extends it with spatial capabilities.

4. **Recommendation / Search Logic**  
   A recommendation component helps match user queries to relevant resources. Depending on the implementation, this may involve embeddings, similarity matching, filtering, or ranking logic.

5. **Background / Messaging Services**  
   RabbitMQ support is included for message passing or asynchronous task handling.

6. **Web Server / Reverse Proxy**  
   Nginx is used to support request routing and static file handling in deployment contexts.

7. **Containerized Environment**  
   Docker and Docker Compose make it possible to run the different services together in a reproducible way across different machines.

This separation is useful because each major part of the system has a defined responsibility. It also improves maintainability, since changes to one part of the project can often be made without rewriting the entire system.

---

## 5. Technology Stack

The project uses a combination of frameworks, services, and developer tools. Understanding why each technology is used helps future developers know where to make changes and how the pieces fit together.

### 5.1 Backend Framework
- **Django**
  - Django is the main web framework used for the backend.
  - It provides URL routing, models, views, templates, admin functionality, and database interaction.
  - Django was a strong choice for this project because it allows rapid development while still supporting scalable and maintainable architecture.

### 5.2 Programming Language
- **Python**
  - Python is the primary language used throughout the backend.
  - It is used because of its readability, strong ecosystem, and compatibility with Django and AI-related tools.

### 5.3 Database
- **PostgreSQL**
  - PostgreSQL is the main relational database used by the system.
  - It is responsible for storing persistent application data.
- **PostGIS**
  - PostGIS adds geospatial support to PostgreSQL.
  - This is important for resource systems where proximity, region, or location-based search may matter.

### 5.4 Containerization and Infrastructure
- **Docker**
  - Docker packages services into containers so the project can run consistently on different machines.
- **Docker Compose**
  - Docker Compose makes it easier to start multiple related services together, such as the application server, database, RabbitMQ, and nginx.

### 5.5 Messaging / Background Services
- **RabbitMQ**
  - RabbitMQ is included to support asynchronous communication or task processing.
  - This is useful in systems where some work should not block user requests.

### 5.6 Web Serving
- **Nginx**
  - Nginx is used as a web server and reverse proxy.
  - It can manage traffic, serve static files, and forward requests to application containers.

### 5.7 Version Control
- **Git / GitHub**
  - Git is used for version control.
  - GitHub is used to host the repository and support collaboration among team members.

---

## 6. Repository and Project Structure

A developer should understand the directory layout before making changes. While the exact structure may change over time, the repository contains several major folders and configuration files that indicate how the project is organized.

### 6.1 Root-Level Structure

At the root of the project, there are directories and files related to the application, deployment, support services, and data setup. Important folders include the following:

- `app/`  
  Contains the main application code and service-related files.

- `carebot/`  
  Contains the main Django project configuration and application logic.

- `chat/`  
  Contains functionality related to chat or conversation-based interactions, if enabled by the project.

- `recommendation/`  
  Contains logic for recommendation, matching, or AI-powered search behavior.

- `nginx/`  
  Contains configuration files for the nginx service.

- `rmq/`  
  Contains files related to RabbitMQ configuration or support.

- `scripts/`  
  Contains helper scripts used for setup, automation, or maintenance tasks.

- `imports/`  
  Contains imported data files such as CSV files or other source data that may be used to populate the system.

- `images/`  
  May contain project diagrams, architecture images, or documentation assets.

- `pgisvector/`  
  Likely contains support for PostGIS vector or spatial database configuration.

### 6.2 Important Configuration Files

Several files are especially important for developers:

- `manage.py`  
  Standard Django entry point used for migrations, running the server, testing, and administrative commands.

- `requirements.txt`  
  Lists Python dependencies required by the project.

- `Dockerfile`  
  Defines how the Docker image for the application is built.

- `docker-compose.yml`  
  Defines the default multi-container configuration.

- `docker-compose.local.yml`  
  Defines local development container setup.

- `docker-compose.gcloud.yml`  
  Appears to support deployment in a cloud environment.

- `.env-local.example`  
  Example environment variable file used as a template for local setup.

- `.env-local`  
  Developer-specific environment file created from the example template.

- `database_dump.sql` / `carebot.sql`  
  SQL dumps that may be used to initialize or restore database data.

### 6.3 Why Structure Matters

The project structure reflects separation of concerns. A developer working on backend logic will spend most of their time in the Django-related folders. A developer working on deployment or configuration will spend more time in Docker, nginx, and environment configuration files. Someone working on search or recommendation behavior will likely spend more time in the `recommendation/` directory.

This separation improves readability and makes it easier to divide work among team members.

---

## 7. Development Environment Setup

This section explains how to set up the project on a local machine. These steps are written with the assumption that a developer is starting from scratch.

### 7.1 Prerequisites

Before attempting setup, make sure the following tools are installed:

- **Git**
  - Used to clone and manage the repository.

- **Docker**
  - Required to run the application and supporting services in containers.

- **Docker Compose**
  - Used to bring up multi-container configurations.

- **Python 3.x** (optional but useful)
  - Helpful if running local commands outside Docker or inspecting dependencies.

- **A code editor or IDE**
  - Recommended: VS Code, PyCharm, or another editor with Python support.

### 7.2 Clone the Repository

First, clone the repository from GitHub:

```bash
git clone <repository-url>
cd carebot-deployable/root

### 7.3 Review Project Files

After cloning the repository, the next step is to inspect the project files and confirm that the expected directories and configuration files are present. This is an important step because it helps verify that the repository was cloned correctly and gives the developer an initial understanding of how the project is organized.

At this stage, developers should look for the main application folders, Docker-related files, environment templates, and any database initialization files. Based on the project structure, important items to confirm include:
- `app/`
- `carebot/`
- `chat/`
- `recommendation/`
- `nginx/`
- `rmq/`
- `scripts/`
- `imports/`
- `images/`
- `pgisvector/`
- `manage.py`
- `requirements.txt`
- `Dockerfile`
- `docker-compose.yml`
- `docker-compose.local.yml`
- `docker-compose.gcloud.yml`
- `.env-local.example`

Reviewing these files early helps developers understand where they will likely need to work depending on their task. For example, someone working on backend logic will likely spend time in the Django application folders, while someone working on configuration or deployment will spend more time with Docker, nginx, and environment files.

### 7.4 Create the Local Environment File

The project includes an example environment variable file that should be copied and used as the basis for the developer’s own local configuration. This is typically done with the following command:

```bash
cp .env-local.example .env-local
```

The `.env-local` file stores environment-specific values that should not always be hardcoded into the application itself. These may include:
- database host and port,
- database name,
- database username and password,
- secret keys,
- debug settings,
- service URLs,
- and application port values.

After copying the file, the developer should open `.env-local` and review each variable carefully. In some cases, the default values may work as-is for local development, but in other cases they may need to be customized based on the developer’s machine, container setup, or database configuration.

This file is especially important because many startup issues in containerized applications happen when an environment variable is missing, spelled incorrectly, or points to the wrong service. For that reason, verifying `.env-local` should always be part of the initial setup process.

### 7.5 Build and Start the Application

Once the environment file is in place, the next step is to build and launch the project using Docker Compose. For local development, this is done with:

```bash
docker-compose -f docker-compose.local.yml up --build
```

This command tells Docker Compose to use the local development configuration file, build the images if needed, and then start all defined services.

Depending on the configuration, this may include:
- the Django application container,
- the PostgreSQL/PostGIS database container,
- the RabbitMQ service,
- the nginx service,
- and any other supporting containers required by the system.

The `--build` flag is important because it ensures that any recent changes to the Dockerfile, dependencies, or configuration are included in the image before the containers start.

The first build may take longer than later runs because Docker may need to:
- download base images,
- install Python dependencies,
- set up services,
- and build the application container from scratch.

Once the command completes successfully, the developer should see logs indicating that the services are starting. If a container fails, Docker will usually print an error message in the terminal.

### 7.6 Confirm Services Are Running

After starting the project, the developer should confirm that all expected containers are actually running. This can be checked with:

```bash
docker ps
```

This command lists all currently running containers. The output should show the containers associated with the CareBot project, along with their container names, status, and exposed ports.

At this stage, the developer should check:
- that the application container is running,
- that the database container is running,
- that supporting services such as RabbitMQ and nginx are also running if required,
- and that no important container has exited unexpectedly.

If one or more containers stop immediately after launch, that usually indicates a configuration or startup error. In that case, the developer should inspect the logs using Docker Compose commands to determine the cause.

Verifying container status early helps prevent confusion later in the setup process. If the containers are not stable, the web application will not function properly.

### 7.7 Access the Application

Once the containers are running successfully, the developer should open the application in a web browser. The address will usually be a localhost URL mapped from the Docker configuration, such as:

```text
http://localhost:<port>
```

The exact port number depends on the project’s environment and Docker Compose configuration. The developer should check the compose file or environment variables if the correct port is unclear.

A successful startup should result in the application loading in the browser. At that point, the developer should verify that:
- the homepage loads,
- the interface renders correctly,
- no obvious server errors appear,
- and key pages or forms are accessible.

If the page does not load, likely causes include:
- incorrect port mapping,
- application startup failure,
- missing migrations,
- database connection issues,
- or an nginx configuration problem.

This step is important because it confirms that the project is not only running at the container level, but is also actually reachable and usable from the browser.

### 7.8 Stop the Development Environment

When development work is finished, the containers can be stopped with:

```bash
docker-compose -f docker-compose.local.yml down
```

This command stops and removes the containers started by the local Docker Compose configuration. It is useful when the developer wants to:
- shut down the project cleanly,
- free up system resources,
- restart the environment later,
- or rebuild the containers after making changes.

Stopping the environment in this way is preferable to closing the terminal abruptly, because it ensures the containers are shut down in an organized and predictable manner.

## 8. Database Setup and Migrations

Because the project relies on PostgreSQL and PostGIS, developers need to understand how the database schema is created and updated. A working database is necessary for the application to store and retrieve data correctly.

### 8.1 Running Migrations

If the database is being set up for the first time, or if model changes have been made, Django migrations may need to be applied. This can typically be done with:

```bash
docker exec -it <app_container_name> python manage.py migrate
```

This command runs the Django migration system inside the application container. Migrations create or update database tables so they match the Django models defined in the codebase.

Running migrations is one of the most important setup steps because the application may fail if it expects tables or columns that do not yet exist.

### 8.2 Loading Initial Data

If the system requires preloaded data, the included SQL dump files such as `database_dump.sql` or `carebot.sql` may need to be imported into PostgreSQL. This is especially useful if the platform depends on existing resources or records in order to demonstrate functionality.

The exact method of importing the SQL dump depends on how the database container is configured, but the purpose is the same: to initialize the system with data so that features such as search, recommendations, or browsing can work correctly.

### 8.3 Importance of PostGIS

The use of PostGIS indicates that spatial or geographic data is part of the system design. This is important for a platform that helps users find local services, since location can strongly affect what resources are relevant.

PostGIS may support functionality such as:
- storing coordinates,
- searching by area or region,
- filtering by distance,
- and enabling location-based recommendations.

If geographic queries are not functioning as expected, the problem may be related to database setup or PostGIS support.

## 9. Running the Application in Development

Once setup is complete, the developer should understand what a successful local development environment looks like. In general, a successful run means:
- all required containers are up,
- the database is connected,
- migrations are applied,
- the application is accessible in a browser,
- and the main functionality loads without major errors.

During development, it is common to monitor the logs while interacting with the application. This helps catch issues such as:
- missing dependencies,
- broken routes,
- failed database queries,
- unhandled exceptions,
- or service communication errors.

Useful commands for logs include:

```bash
docker-compose -f docker-compose.local.yml logs
```

and for continuous log output:

```bash
docker-compose -f docker-compose.local.yml logs -f
```

These commands are especially valuable during debugging because they allow the developer to see what is happening behind the scenes as requests are made.

## 10. Key Functional Components

This section explains the most important technical components of the CareBot platform so that a new developer understands how the major features are divided across the system.

### 10.1 Crowdsourcing Functionality

One of the most important aspects of the platform is its crowdsourcing model. Instead of relying on a fixed internal dataset, the platform allows resource data to be submitted, stored, and managed over time.

From a development perspective, this likely involves:
- submission forms,
- validation logic,
- database storage,
- update workflows,
- and possibly review or moderation processes.

This functionality is central to the purpose of the project because community-submitted information allows the platform to expand and stay relevant as new resources become available.

### 10.2 Search and Recommendation System

The recommendation or search component is another core part of the system. Its purpose is to help users find relevant resources based on their needs, even if they do not know the exact service name or category.

This part of the project may involve:
- semantic matching,
- ranking logic,
- query interpretation,
- filtering,
- or AI-powered recommendation strategies.

The `recommendation/` directory likely contains much of this logic. Developers working in this area should take care to understand how user input is transformed into relevant resource results.

### 10.3 Chat or Conversational Support

The presence of a `chat/` directory suggests that the platform may include conversational features. These could allow users to interact with the system more naturally by asking for help in plain language.

From a development standpoint, this may involve:
- handling user input,
- passing requests to recommendation logic,
- retrieving data,
- and formatting responses.

If this feature is active, it may connect multiple parts of the system and should therefore be changed carefully.

### 10.4 Geospatial Resource Handling

Because the project uses PostGIS, geographic data is likely an important part of the application. This may support location-aware searching and filtering so that users can find nearby services.

A developer working on these features should understand:
- how location data is stored,
- how geographic queries are performed,
- and how the frontend or backend uses this information when displaying results.

This functionality is especially useful for a resource platform because proximity often matters when recommending support services.

### 10.5 Background Processing and Messaging

RabbitMQ support suggests that the project includes asynchronous or message-based processing. This can improve system performance by moving heavier or non-immediate tasks out of the main request-response cycle.

Examples of asynchronous tasks could include:
- recommendation processing,
- notification handling,
- data imports,
- or service communication workflows.

If developers are working in this area, they should make sure both the application and RabbitMQ services are running and properly connected.

## 11. Developer Workflow

In addition to setup, developers should follow a consistent workflow when contributing to the repository. This helps keep changes organized and reduces confusion when multiple people are working on the same project.

### 11.1 Pull the Latest Changes

Before starting new work, developers should make sure they have the latest version of the repository:

```bash
git pull origin main
```

If the primary branch uses a different name, that name should be substituted accordingly.

### 11.2 Create a Separate Branch

Developers should avoid making direct changes on the main branch. Instead, they should create a separate branch for each feature, bug fix, or documentation change.

Suggested naming conventions include:
- `feature/<name>`
- `bugfix/<name>`
- `docs/<name>`

Examples:
- `feature/improve-resource-search`
- `bugfix/fix-db-connection`
- `docs/update-development-guide`

This makes collaboration easier and keeps changes easier to review.

### 11.3 Make and Verify Changes

After making modifications, developers should run the project locally and verify that the affected functionality still works. This includes checking the browser, reviewing logs, and making sure no new errors were introduced.

### 11.4 Commit Clearly

Commit messages should describe what changed in a meaningful way. Good examples include:
- `Add validation for resource submission`
- `Fix Docker configuration for local environment`
- `Update development guide setup steps`

Clear commit history makes the project easier to maintain later.

### 11.5 Push and Open a Pull Request

When changes are ready, the branch should be pushed to GitHub:

```bash
git push origin <branch-name>
```

A pull request should then be created with:
- a summary of the changes,
- notes on how the change was tested,
- and any additional context reviewers may need.

## 12. Code Quality and Contribution Expectations

Maintaining readable and consistent code is important for long-term project health, especially when multiple developers contribute over time.

### 12.1 Write Readable Code

Developers should use clear function names, descriptive variables, and manageable function sizes. Code should be easy for another team member to read and understand.

### 12.2 Follow Existing Patterns

Before introducing a new style or structure, developers should review how similar functionality is already implemented elsewhere in the project. Consistency makes the codebase easier to maintain.

### 12.3 Keep Changes Focused

Each branch and pull request should address a specific issue, feature, or documentation improvement. This makes review and debugging easier.

### 12.4 Update Documentation

If a developer changes setup instructions, project structure, or a major workflow, the relevant documentation should be updated as well.

### 12.5 Use Review Before Merge

Whenever possible, changes should be reviewed by another team member before merging. Code review helps catch mistakes and improves shared understanding of the project.

## 13. Summary

The CareBot Crowdsourcing Platform is a multi-service web application built to help users discover senior support resources more effectively. From a development perspective, the project combines a Django backend, PostgreSQL/PostGIS database support, recommendation features, Docker-based infrastructure, and service-oriented configuration.

For a new developer, the most important onboarding steps are:
1. clone the repository,
2. review the project files,
3. create the `.env-local` file,
4. launch the local development environment with Docker Compose,
5. verify that services are running,
6. apply migrations if needed,
7. confirm the application opens in the browser,
8. and use logs to debug any issues.

The project structure separates major concerns such as backend logic, recommendation features, messaging, web serving, and infrastructure. This organization helps make development work clearer and easier to divide among team members.

Overall, this guide is intended to reduce onboarding difficulty, improve maintainability, and provide future developers with a clear path for understanding and contributing to the project.
