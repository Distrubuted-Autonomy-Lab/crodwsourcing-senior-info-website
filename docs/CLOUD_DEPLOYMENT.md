# Deployment of Carebot to the Google Cloud Platform

## Overview

This guide details the process required to deploy this application to the Google Cloud Platform
(GCP) using Docker compose, Docker containers, and the google cloud SDK. The deployment provides
three main services: the application logic implemented as a Django web application, an Nginx proxy,
and a PostgreSQL with PostGIS and pgvector extensions.

## Deployment

### Prerequisites

- `Google Cloud SDK` (`gcloud`)

### Steps

#### 1. Configure `Google Cloud SDK` CLI

First, we must ensure that we are logged into the account with which the deployment should be
associated. Run the below script (it should open a tab in your browser).

```bash
gcloud auth login
```

Select the Google account with which the cloud deployment should be associated. Next, read and
accept the request for permissions from the Google Cloud SDK.

#### 2. Create and configure the GCP project

Before we can create a virtual machine on which the deployment will run, we must create a GCP
project to run the deployment under. This will create an association for billing and organization
of cloud configuration and resources. We also will set this project as the default.

```bash
gcloud projects create <project-id> --name="<project-name>"
gcloud config set project <project-id>
```

Before we continue, a billing account must be assigned to the project. To list billing accounts,
run `gcloud billing accounts list`. To assign a billing account to the project, run `gcloud beta
billing projects link <project-id> --billing-account=<billing-account-id>`.

Now we can configure the project as desired.

```bash
# Enable the GCP Compute Engine service (This will likely take a while)
gcloud services enable compute.googleapis.com
```

#### 3. Create and configure the virtual machine

Before deploying the project, we must create and configure a GCP Compute Engine instance and
prepare it with the necessary configuration and updates.

##### A. Create the startup script

The startup script will be run every time the Compute Engine instance boots. Create the following
file:

```bash
#!/bin/bash

set -euo pipefail

# Configure startup script logging.
LOG=/var/log/startup.log
exec > >(tee -a "$LOG") 2>&1

echo "=== Startup Script Beginning ==="

# Update and install prerequisites.
apt-get update
apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release

# Add Docker's GPG key & repository.
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

## Add the repository to APT sources.
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker and Docker compose.
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

echo "=== Startup Script Finished ==="
```

##### B. Create the Compute Engine instance

Execute the below command to create a GCP Compute Engine instance with the above startup script
integated:

```bash
# Instance type of E2-medium is based on our needs being fairly general.
# The tags are used for firewall configuration.
gcloud compute instances create <instance-name> \
    --project=<project-id> \
    --zone=us-central1-a \
    --machine-type=e2-medium \
    --image-family=ubuntu-2204-lts \
    --image-project=ubuntu-os-cloud \
    --boot-disk-size=30GB \
    --tags=http-server,https-server \
    --metadata-from-file=startup-script=./<startup-script>
```

Next, configure the firewalls as required:

```bash
# Enables HTTP traffic.
gcloud compute firewall-rules create allow-http \
    --action=ALLOW \
    --rules=tcp:80 \
    --source-ranges=0.0.0.0/0 \
    --target-tags=http-server \
    --description="Enable HTTP traffic"

# Only run if the project has been updated to support HTTPS.
# It doesn't as of April 2026.
gcloud compute firewall-rules create allow-https \
    --action=ALLOW \
    --rules=tcp:443 \
    --source-ranges=0.0.0.0/0 \
    --target-tags=https-server \
    --description="Enable HTTPS traffic"
```

These operations might not be instant and there may still be execution occurring after the command
returns. Any operations in future steps might need to wait until such operations complete.

#### 4. Prepare Cloud Environment Variables

Create a `.env-gcloud` based on [`.env-gcloud.example`](../../root/.env-gcloud.example). The
example file will require modifications before it is used. There are instructions included in
said example file.

#### 5. Upload Code and Clouad Environment Variables to Compute Engine Instance

Transfer the application code and `.env-gcloud` file to the GCP instance:

This can be accomplished in one of two manners:

```bash
gcloud compute ssh <instance-name> --command="mkdir -p ~/carebot"
gcloud compute scp --recurse . <instance-name>:~/carebot/
gcloud compute scp --recurse .env-gcloud <instance-name>:~/carebot/root/
```

Or if the project is available over `git`:

```bash
# SSH into the instance.
gcloud compute ssh <instance-name>

git clone <repository-url> ~/carebot/
cd ~/carebot/root
```

and then make sure that the environment file is placed at `~/carebot/root/.env-gcloud`.

#### 6. Prepare for GCP Compute Engine Project Deployment

SSH into the GCP Compute Engine instance:

```bash
gcloud compute ssh <instance-name>
```

and then move to the application root directory:

```bash
cd ~/carebot/root/
```

#### 7. Deploy the project using Docker Compose

Deploy the project using Docker compose by running the following command:

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml up -d
```

#### 8. Initialize database/Django configuration

After all services are running, database migrations may need to be run:

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml exec web python manage.py migrate
```

A Django superuser may need to be created if one has not been created yet:

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml exec web python manage.py createsuperuser
```

## Troubleshooting

### Applicaton Errors

1. Ensure API keys are valid and active.
2. Validate the `SECRET_KEY` and `ALLOWED_HOSTS` configurations.
3. View the application's logs ([Guide](#monitoring)).

## Database Errors

1. Validate that the PostgreSQL service is running: `docker ps` (look for `web` or `root-web`).
2. Validate that the database username and password are the same between web and postgreSQL.

## Security/Reliability Considerations

1. `.env-gcloud` should remain private at all times.
2. Use GCP firewalls to restrict external interaction to the minimum.
3. Regularly update the Docker images.
4. Ensure that regular backups of the PostgreSQL database are completed.

## Additional Deployment Details

### Volumes/Persistent Storage

The deployment uses several named Docker volumes:

```yaml
volumes:
    static_volume: # Nginx static files
    media_volume: # Media files
    postgis_data: # PostgreSQL database files
```

These volumes are persistent across container interactions and restarts.

### External Port Configuration

- Port 80 (HTTP): Nginx reverse proxy

## Monitoring

To view logs:

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml logs -f web
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml logs -f nginx
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml logs -f postgis
```

## Maintenence

To stop the provided services:

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml down
```

To restart the services without rebuilding:

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml restart
```

To backup the database:

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml exec postgis pg_dump -U <your-db-user> <your-database-name> > backup.sql
```

To restore the database from a backup:

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.gcloud.yml exec postgis psql -U <your-db-user> <your-database-name> < backup.sql
```
