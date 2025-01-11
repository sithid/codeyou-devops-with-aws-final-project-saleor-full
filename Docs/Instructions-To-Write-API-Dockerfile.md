### Instructions for Writing the Saleor API Dockerfile

#### Overview
This guide will help you write a Dockerfile to containerize the Saleor API. The Dockerfile will:
- Install necessary system dependencies.
- Set up Python and Poetry for managing dependencies.
- Configure the environment and collect static files.
- Prepare the application to run with `gunicorn`.

You are encouraged to follow these instructions step-by-step and understand the purpose of each section in the Dockerfile.

---

### Steps to Write the Dockerfile

The location of this dockerfile should be at `<This-Repo-Root>/saleor-api/Dockerfile`

#### Step 1: Specify the Base Image
- Use `python:3.12` as the base image to ensure compatibility with Saleor and its dependencies.
   - This image includes Python 3.12 and the required tools for Python application development.

#### Step 2: Install System Dependencies
- Add system libraries needed for the Saleor API:
   - Use `apt` to install dependencies such as:
     - `gettext`: For translations.
     - Libraries for Pillow:
       - `libffi8`
       - `libgdk-pixbuf2.0-0`
       - `liblcms2-2`
       - `libopenjp2-7`
       - `libssl3`
       - `libtiff6`
       - `libwebp7`
       - `shared-mime-info`
       - `mime-support`
     - PostgreSQL client library:
       - `libpq5`
   - Clean up `apt` cache to minimize image size.

#### Step 3: Set Up Python Dependencies
- Use Poetry for dependency management:
   - Install Poetry with a specific version (`1.8.4`).
   - TODO: needs more context. Configure Poetry to install dependencies directly in the system environment (disable virtual environments).
   - Copy `pyproject.toml` and `poetry.lock` into the image.
   - Run `poetry install --no-root` to install dependencies.

#### Step 4: Create a Non-Root User
-  Add a non-root user for running the application:
   - Use `useradd` to create a `saleor` user and group.
   - Ensure ownership of the `/app` directory is assigned to this user.

#### Step 5: Configure Static Files
- Collect static files for the application:
   - Use Django's `collectstatic` command which is listed in the `manage.py`.
   - Configure `ARG` and `ENV` for the `STATIC_URL`:
     - Add an `ARG` for `STATIC_URL`; this will be a default value for the environment variable.
     - Use an `ENV` to set the runtime value of `STATIC_URL` using the `ARG`, default value should be `/static/`.
   - Example:
     ```Dockerfile
     ARG STATIC_URL
     ENV STATIC_URL=${STATIC_URL:-/static/}
     RUN SECRET_KEY=dummy STATIC_URL=${STATIC_URL} python3 manage.py collectstatic --no-input
     ```

#### Step 6: Expose Application Ports
- Expose port `8000` to allow external access to the Saleor API.

#### Step 7: Add the Start Command
- Specify the command to run the application using `CMD`:
   - Use `gunicorn` to serve the Saleor API:
     ```json
     CMD ["gunicorn", "--bind", ":8000", "--workers", "4", "--worker-class", "saleor.asgi.gunicorn_worker.UvicornWorker", "saleor.asgi:application"]
     ```

#### Step 8: Test the Dockerfile
- Build the Docker image:
   ```bash
   docker build -t saleor-api:<tag> ./saleor-api
   ```
- Run the container:
   ```bash
   docker run -p 8000:8000 saleor-api:<tag>
   ```
- Verify the application is running at `http://localhost:8000`.

---

### Key Points to Remember

1. **Avoid Multi-Stage Builds**:
   - In this case we are trying to keep the Dockerfile simple and use a single stage for building and running the application. Typically it would make sense to use a multi-stage build in order to keep the images as small as possible. If you'd like to attempt using a multi-stage you're welcome to do so.

2. **Understand ARG and ENV**:
   - Use `ARG` for build-time variables and `ENV` for runtime variables.
   - Example:
     - `ARG STATIC_URL` defines the build argument variable at build time.
     - `ENV STATIC_URL=${STATIC_URL:-/static/}` sets a default runtime value for `STATIC_URL`.
     - This will allow us to pass in static URL at build time that becomes an environment variable, or by default will use `/static/` as the value of the ennvironment variable if we do not pass in a build argument at build time.

3. **Use Non-Root Users**:
   - Running applications as a non-root user improves security and if often required by organizations to maintain security compliance.

4. **Clean Up**:
   - Remove unnecessary files and cache (e.g., `apt` cache) to keep the image size small.

---

By following these steps, you will learn to write a Dockerfile that builds and runs the Saleor API efficiently while understanding key Dockerfile concepts like environment variables, dependency management, and application startup commands.

