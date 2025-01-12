### Saleor API CI/CD Pipeline Requirements for Final Project

#### **General Requirements**
- Students must create a **CI/CD pipeline** for each of the three primary branches (`develop`, `staging`, and `production`), aligning with the following requirements.

---

### **Develop Branch**
#### Purpose:
- Represents the "development" environment for active coding and iteration.

#### Pipeline Requirements:
1. **Static Code Analysis**:
   - Use a linter (e.g., Ruff or Flake8) to ensure code style and quality.
   - Findings should not fail the pipeline but must be logged. Expect that there may be failures.

2. **Dependency Analysis**:
   - Use tools like `pip-audit` to analyze dependencies for vulnerabilities.
   - Findings should not fail the pipeline but must be logged.

3. **Build the Docker Image**:
   - Build the application Docker image from the provided `Dockerfile`.

4. **Run Health Check**:
   - **Optional**: Temporarily start the containers for any images (saleor-api, saleor-dashboard) that you are building and test its health using:
     - A specific API endpoint (e.g., `/graphql`).
     - Accept HTTP response codes 200 (OK) or 404 (Not Found) as healthy.

5. **Trivy Scan**:
    - Use Trivy to scan the built image for vulnerabilities.

5. **Push to DockerHub**:
   - Deploy the image to DockerHub tagged as `<your-app-name>:develop`.

---

### **Staging Branch**
#### Purpose:
- Represents the "staging" environment for testing and integration.

#### Pipeline Requirements:
1. **Compose Validation**:
   - Perform a `docker-compose up` to validate the stack.
   - Use health checks defined in the `docker-compose.yml` file to verify service readiness, or build your own in the pipeline during the Compose Validation.
   - Failure to validate that the compose stack is up and working should fail the pipeline.

2. **Build the Docker Image**:
   - Build the application Docker image.

3. **Push to DockerHub**:
   - Deploy the image to DockerHub tagged as `<your-app-name>:staging`.

---

### **Production Branch**
#### Purpose:
- Represents the "production" environment for deployment to AWS.

#### Pipeline Requirements:
1. **Ensure Staging Completeness**:
   - Confirm all tests and checks from `staging` are successful before merging.

2. **Build the Docker Image**:
   - Build the application Docker image.

3. **Push to DockerHub**:
   - Deploy the image to DockerHub tagged as `<your-app-name>:production`.
   - Maintain a `latest` tag to reflect the production image for consistency and reliability.

4. **AWS Deployment**:
   - Use the `production` branch to trigger deployment to AWS.

---

### **Vulnerability Scanning**
- Use Trivy to scan Docker images in the pipeline.
  - Run the scan after building the image but before pushing it to DockerHub.
  - Findings from Trivy should not fail the pipeline but must be logged for review.

---

### **Example Tools and Commands**
- **Static Code Analysis**:
  ```bash
  ruff check .
  ```

- **Dependency Analysis**:
  ```bash
  pip-audit
  ```

- **Build Docker Image**:
  ```bash
  docker build -t <your-app-name>:<tag> .
  ```

- **Push to DockerHub**:
  ```bash
  docker login -u <DOCKERHUB_USERNAME> -p <DOCKERHUB_TOKEN>
  docker push <your-app-name>:<tag>
  ```

- **Trivy Vulnerability Scan**:
  ```bash
  trivy image <your-app-name>:<tag>
  ```

