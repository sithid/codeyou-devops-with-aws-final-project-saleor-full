## Deploying Saleor Final Project
### Instructions - Build a Docker Compose File for the Saleor Stack


#### Overview

This `docker-compose.yml` defines a multi-service application environment for a Saleor-based project. Saleor is a headless e-commerce platform with an API, dashboard, and supporting services such as databases, caches, and monitoring tools. Each service plays a unique role in the ecosystem. Follow these steps to build your final project while understanding how each service fits into the big picture.

---

### Step 1: Create the Project Directory Structure

1. Organize your files for clarity:
    - Ensure you have a `docker-compose.yml` file in the root directory.

---

### Step 2: Define the API Service

1. **Purpose**: The `api` service is the core backend of the Saleor application. It serves as the GraphQL API for the e-commerce platform.
2. **Key Configurations**:
    - **Dockerfile**: The API image should be built from the `./saleor-api/Dockerfile`. Ensure the Dockerfile is located at this path, refer to [these docs on writing the API Dockerfile](./Instructions-To-Write-API-Dockerfile.md). This should be built in Docker-Compose. You'll also need to push this to Dockerhub for later use.
    - **Ports**: Exposes port `8000` for access to the API.
    - **Dependencies**: Relies on the database (`db`), cache (`redis`), and monitoring (`jaeger`).
    - **Volumes**: Shares media files between the API and worker services. The `/app/media` directory should be mounted in a volume.
    - **Environment Variables**: Configure allowed hosts (`localhost,api,dashboard`), Jaeger monitoring, and dashboard URLs. Investigate the saleor-api application environment variables required to connect to the Jaeger Agent Host. This is part of the student's exploration.

---

### Step 3: Add the Dashboard Service

1. **Purpose**: The `dashboard` provides a user interface for managing the e-commerce store.
2. **Key Configurations**:
    - **Dockerfile**: The Dashboard image should be built from the `./saleor-dashboard/Dockerfile`. Ensure the Dockerfile is located at this path, refer to [these docs on writing the Dashboard Dockerfile](./Instructions-To-Write-Dashboard-Dockerfile.md). This should be built in Docker-Compose. You'll also need to push this to Dockerhub for later use.
    - **Ports**: Exposes port `9000` to serve the dashboard.
    - **Dependencies**: Requires the `api` service for backend communication.
    - **Configuration**: Make sure that this service restart is set to unless-stopped.

---

### Step 4: Configure the Database Service

1. **Purpose**: The `db` service uses PostgreSQL to store all application data.
2. **Key Configurations**:
    - **Image**: PostgreSQL database image. Be sure to use `postgres:15-alpine`.
    - **Ports**: Exposes port `5432` for database access.
    - **Volumes**: Persists data using the `saleor-db` volume and initializes with a custom script.
        - Persist data: Mount the `/var/lib/postgresql/data` into a named volume.
        - Copy an initialization script: using a bind-mount technique mount the `replica_user.sql` script to the location that PostgreSQL specifies for initializing the database.
    - **Environment Variables**: Define user credentials (`POSTGRES_USER`, `POSTGRES_PASSWORD`).
    - **Configuration**: Make sure that this service restart is set to unless-stopped.

---

### Step 5: Set Up the Redis Service

1. **Purpose**: The `redis` service provides caching for faster data retrieval and supports tasks like Celery workers.
2. **Key Configurations**:
    - **Image**: Redis image for caching. You can use `redis:7.0-alpine`
    - **Ports**: Exposes port `6379` for access.
    - **Volumes**: Use a named volume to mount `/data` for data persistence.
    - **Configuration**: Make sure that this service restart is set to unless-stopped.

---

### Step 6: Add the Jaeger Monitoring Service (Bonus Task)

1. **Purpose**: The `jaeger` service enables distributed tracing to monitor and debug the system.
2. **Key Configurations**:
    - **Image**: Use the Jaeger all-in-one image, `jaegertracing/all-in-one:1.20.0`.
    - **Ports**: Investigate the image layers to determine required port mappings, which include both TCP and UDP ports.
    - **Volumes**: Use temporary storage (`tmpfs`) mounted at volume location specified in the docker image layers
    - **Dependencies**: Ensure the `api` service is running before Jaeger can perform tracing. 
    - **Configuration**: Make sure that this service restart is set to unless-stopped.

---

### Step 7: Include the Mailpit Service (Bonus Task)

1. **Purpose**: The `mailpit` service provides a mock SMTP server for testing email functionality.
2. **Key Configurations**:
    - **Image**: Mailpit image for email testing, `axllent/mailpit`.
    - **Ports**: Refer to the image documentation to determine the correct ports.
    - **Translation**: Students must interpret and translate the documentation regarding instructions on how to run as a container into a proper Docker Compose service configuration.
    - **Configuration**: Make sure that this service restart is set to unless-stopped.

---

### Step 8: Define Volumes

1. **Purpose**: Volumes persist data across container restarts and allow services to share files.
2. **Notes**: You can name the named volumes whatever you want, these are some recommended naming schemas.
3. **Key Configurations (Recommendations)**:
    - `saleor-db`: Stores database data.
    - `saleor-redis`: Stores cache data.
    - `saleor-media`: Shares media files between the API and worker services.

---

### Step 9: Configure Networks

1. **Purpose**: Networks ensure isolated communication between services.
2. **Key Configurations**:
    
    - **Bridge Network**: Create the `saleor-backend-tier` network which allows all services to communicate internally while remaining isolated from other networks. **Note**: Usually we use the `default` network instead. This `saleor-backend-tier` should use `bridge` network mode and each service should specify the `saleor-backend-tier`. Example:
    
    ```
    someService:
     image: someService:1.0-alpine
     ...
     networks:
         - saleor-backend-tier
    ```
---

### Step 10: Add Healthchecks for Services

1. **Purpose**: Healthchecks ensure that services are running as expected and help Docker manage container restarts in case of failures.
2. **Instructions**:
    - Add a `healthcheck` to each service in the `docker-compose.yml`. Examples:
      - **API Service**:
        ```yaml
        healthcheck:
          test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
          interval: 30s
          timeout: 10s
          retries: 3
        ```
      - **Dashboard Service**: You should be able to use curl to check that the Dashboard service is ready to be used.
      - **Database**: You should be able to use a built-in postgres function (`pg_isready`) that tests if postgres is up
      - **Redis Service**: You should be able to use the redis-cli to ping the redis service.
      <!-- - **Database Service**:
        ```yaml
        healthcheck:
          test: ["CMD", "pg_isready", "-U", "postgres"]
          interval: 30s
          timeout: 10s
          retries: 3
        ```
      - **Redis Service**:
        ```yaml
        healthcheck:
          test: ["CMD", "redis-cli", "ping"]
          interval: 30s
          timeout: 10s
          retries: 3
        ``` -->

---

### Step 11: Initialize and Start the Application

1. We are using shared folders to enable live code reloading. Without this, Docker Compose will not start:
    
    - **Windows/MacOS**: Add the cloned `saleor-platform` directory to Docker shared directories (Preferences -> Resources -> File sharing).
    - **Windows/MacOS**: Ensure that in Docker preferences, at least 5 GB of memory is allocated (Preferences -> Resources -> Advanced).
    - **Linux**: No action is required, as sharing is already enabled and memory for the Docker engine is not limited.
2. Navigate to the cloned directory:
    
    ```shell
    cd saleor-platform
    ```
    
3. Build the application:
    
    ```shell
    docker compose build
    ```
    
4. Apply Django migrations:
    
    ```shell
    docker compose run --rm api python3 manage.py migrate
    ```
    
5. Populate the database with example data and create the admin user:
    
    ```shell
    docker compose run --rm api python3 manage.py populatedb --createsuperuser
    ```
    
    _Note_: The `--createsuperuser` argument creates an admin account for `admin@example.com` with the password set to `admin`.
    
6. Run the application:
    
    ```shell
    docker compose up
    ```
    

---

### Step 11: Test the Application

1. Access the API at `http://localhost:8000/`.
2. Open the dashboard at `http://localhost:9000/`.
3. Verify database connectivity by inspecting PostgreSQL logs.
4. Test email functionality through the Mailpit web UI at `http://localhost:8025/`.
5. Explore tracing using Jaeger at `http://localhost:16686/`.

---

### Step 12: Stop and Restart Services

1. Stop the application using:
    
    ```bash
    docker-compose down
    ```
    
2. Restart with:
    
    ```bash
    docker-compose up
    ```
    

By completing these steps, you will build a fully functional Saleor environment, gain hands-on experience with Docker Compose, and understand how services interact within a complex application.
