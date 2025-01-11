### Instructions for Writing the Saleor Dashboard Dockerfile

#### Overview
This guide will help you write a Dockerfile to containerize the Saleor Dashboard. The Dockerfile will:
- Install necessary dependencies for building and serving the Saleor Dashboard.
- Use environment variables to configure the application.
- Optimize the build using a two-stage approach: building the application in a `node` container and serving it with `nginx`.

You should follow these steps and understand the purpose of each section.

---

### Steps to Write the Dockerfile

#### Step 1: Specify the Builder Stage
1. Use `node:18-alpine` as the base image for building the Saleor Dashboard.
   - This lightweight image is optimized for Node.js applications.
   - Example:
        ```Dockerfile
        FROM python:3.12-slim as builder # This designates as the `builder` stage
        ```
2. Add `bash` to the image:
   - Use `apk` to install `bash` for compatibility with scripts used during the build process.

#### Step 2: Set Up the Working Directory
1. Define the working directory for the build process:
   - Use `/app` as the working directory to organize the application files.

#### Step 3: Manage Dependencies
1. Copy `package.json` and `package-lock.json` (or `yarn.lock`) into the working directory.
2. Set the `CI` environment variable to `1` to avoid prompts during the build.
3. Install the dependencies using `npm ci`:
   - Use the `--legacy-peer-deps` flag to ensure compatibility with older dependencies.

#### Step 4: Copy Application Files
- Copy the required files and directories into the image:
   - `nginx/`: Contains configuration files for the `nginx` server.
   - `assets/`: Contains static assets like images and fonts used by the application.
   - `locale/`: Contains language files for localization.
   - `scripts/`: Contains build or runtime scripts.
   - `src/`: Contains the main application source code.
   - Additional configuration and data files:
     - `vite.config.js`: Vite configuration for building the application.
     - `tsconfig.json`: TypeScript configuration file.
     - `*.d.ts`: TypeScript declaration files.
     - `schema.graphql`: GraphQL schema used by the application.
     - `introspection.json`, `introspection*.json`: Introspection files for API queries.
     - `.featureFlags/`: Contains feature flag configurations.

#### Step 5: Configure Environment Variables
1. Use `ARG` and `ENV` to set and manage environment variables:
   - The following Build-Time variables (ARG) should be defined and bound to the respecitve environment variable:
     - **Build-Time Variables (ARG):**
       - `API_URL`: The URL for the API backend, default: `<Your API address>/graphql/`.
       - `APP_MOUNT_URI`: The URI where the dashboard is mounted, default: `/dashboard/`.
       - `APPS_MARKETPLACE_API_URL`: The URL for the apps marketplace API, default: `https://apps.saleor.io/api/v2/saleor-apps`.
       - `APPS_TUNNEL_URL_KEYWORDS`: Keywords for tunneling URLs, no default value.
       - `STATIC_URL`: The base URL for static assets, default: `/dashboard/`.
       - `SKIP_SOURCEMAPS`: Whether to skip source maps generation, default: `true`.
       - `LOCALE_CODE`: The default locale code, default: `EN`.
    - **Example**:
        ```Dockerfile
            # This allows a build argument of API_URL
            # and binds that to the corresponding environment variable 
            # with a default value should the build arg be empty
            ARG API_URL
            ENV API_URL=${API_URL:-http://localhost:8000/graphql/}
        ```

#### Step 6: Build the Application
1. Run the build process with `npm run build`:
   - This compiles the application and outputs the production-ready files.

---

#### Step 7: Specify the Runner Stage
1. Use `nginx:stable-alpine` as the base image for serving the application, this will be the `runner` stage.
   - This image is optimized for lightweight and high-performance web serving.

#### Step 8: Configure the Runner Stage
1. Set the working directory to `/app`.
2. Copy the following files from the `builder` stage:
   - Built application files: Copy from `/app/build/` in the builder stage to `/app/` in the runner stage.
   - Nginx configuration: Copy to `/etc/nginx/conf.d/default.conf`.
   - Environment variable script: Copy to `/docker-entrypoint.d/50-replace-env-vars.sh`.
    ```Dockerfile
        COPY --from=builder /app/build/ /app/
        COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
        COPY ./nginx/replace-env-vars.sh /docker-entrypoint.d/50-replace-env-vars.sh
    ```

#### Step 9: Test the Dockerfile
1. Build the Docker image:
   ```bash
   docker build -t saleor-dashboard ./saleor-dashboard
   ```
2. Run the container:
   ```bash
   docker run -p 9000:80 saleor-dashboard
   ```
3. Verify the dashboard is running at `http://localhost:9000`.

---

### Key Points to Remember

1. **Two-Stage Builds**:
   - The first stage (`builder`) compiles the application.
   - The second stage (`runner`) serves the application using `nginx`.

2. **Environment Variables**:
   - Use `ARG` for build-time variables and `ENV` for runtime defaults.
   - Example:
     ```Dockerfile
     ARG STATIC_URL
     ENV STATIC_URL=${STATIC_URL:-/dashboard/}
     ```

3. **Clean Build Artifacts**:
   - Only copy the necessary files into the `runner` stage to reduce image size.

4. **Dynamic Configuration**:
   - Use the `replace-env-vars.sh` script to inject environment variables into the served application at runtime.

---

By following these steps, you will learn to write a Dockerfile that builds and serves the Saleor Dashboard efficiently, while understanding the principles of multi-stage builds and environment variable management.

