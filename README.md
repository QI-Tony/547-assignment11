# MLflow Model Deployment Documentation

This document provides a comprehensive guide on deploying MLflow models on a remote server using a DigitalOcean (DO) droplet. The deployment process is described below:

---

## Deploy MLflow Model on a DigitalOcean Droplet

### Overview

The deployment involves deploying the MLflow inference server on a DigitalOcean droplet using Docker. This approach ensures that the model is accessible remotely and is suitable for production environments.

### Key Steps

#### 1. Create a DigitalOcean Droplet

- Log in to the MIS547 DigitalOcean account.
- Follow the instructions from previous assignments to set up a new droplet.
- Configure SSH-based authentication.

#### 1. Initialize Server Environment

- Use SSH to connect to the server (Ubuntu) and ensure it is configured correctly.
- Install Docker using the following commands:
  ```bash
  sudo apt update
  sudo apt install -y docker.io
  sudo systemctl start docker
  sudo systemctl enable docker
  ```
- Verify the Docker installation:
  ```bash
  docker --version
  ```

2. **Set Up MLflow Tracking Server**

- Transfer necessary dependency files, such as `Dockerfile` and `docker-compose.yml`, to the server. You can use `scp` or clone them from a Git repository.

  ```bash
  # Using scp:
  scp /path/to/Dockerfile /path/to/docker-compose.yml user@<droplet_ip>:~/project-directory/

  # Using Git:
  ssh user@<droplet_ip>
  git clone <repository_url>
  ```

- Create a Spaces Bucket

  - Log in to your DigitalOcean account and navigate to the "Spaces" section.
  - Create a new Spaces bucket with a unique name (e.g., `mlflow-bucket-tony`).
  - After creating the bucket, generate an access key and secret access key for programmatic access.
  - Save the following strings for future use:
    - `access_key_id`
    - `secret_access_key`
    - `endpoint_url` (e.g., `https://<bucket-name>.<region>.digitaloceanspaces.com`)


- Create a Managed PostgreSQL Database

  - Log in to your DigitalOcean account and navigate to the "Databases" section.
  - Create a new PostgreSQL database and configure it with the desired name, user, and password.
  - Save the following strings for future use:
    - `your_postgres_user`
    - `postgres_password`
    - `postgres_db`

- Create a `.env` file in the project directory and populate it with the following environment variables:

  ```env
  AWS_ACCESS_KEY_ID=<your_aws_access_key_id>
  AWS_SECRET_ACCESS_KEY=<your_aws_secret_access_key>
  MLFLOW_S3_ENDPOINT_URL=<your_s3_endpoint_url>

  POSTGRES_USER=<your_postgres_user>
  POSTGRES_PASSWORD=<your_postgres_password>
  POSTGRES_DB=<your_postgres_db>
  ```

- Run the following command to start the services defined in the `docker-compose.yml` file:

  ```bash
  docker compose up
  ```

#### 3. Test the Remote Endpoint

- Use tools like `curl` or Python to send requests to the droplet’s IP address:
  ```bash
  curl http://<droplet_ip>:5000/invocations -H 'Content-Type: application/json' -d '{"inputs": {"feature_1": 0.25, "feature_2": 0.52}}'
  ```
