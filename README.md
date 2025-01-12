# Saleor Platform Project

Welcome to the Saleor Platform Project! This project involves deploying a fully functional Saleor-based e-commerce platform. Saleor is a modern, open-source, headless e-commerce platform that provides a robust GraphQL API and a user-friendly dashboard for managing your online store.

This README serves as the entry point for students who have chosen the Saleor project to deploy. It includes links to key documentation and instructions to get started.

---

## Table of Contents

1. [Instructions to Containerize the Dashboard](./Docs/Instructions-To-Write-Dashboard-Dockerfile.md)
2. [Instructions to Containerize the API](./Docs/Instructions-To-Write-API-Dockerfile.md)
3. [Instructions to Deploy Compose Stack](./Docs/Instructions-To-Deploy-Compose.md)
4. [Saleor Platform Documentation](Docs/Saleor-Platform-docs.md)
5. [Saleor Overview](Saleor.md)

---

## Project Overview

This project is designed to teach you the fundamentals of deploying, managing, and interacting with a distributed application using Docker and Docker Compose. By completing this project, you will:

- Deploy the Saleor API (GraphQL-based backend).
- Set up the Saleor Dashboard for store management.
- Configure supporting services such as PostgreSQL, Redis, and optional monitoring and email tools (Jaeger and Mailpit).
- Understand container orchestration and resource management.

---

## Quick Start

Follow the instructions below to get started:

1. Fork this repo.

2. Ensure you have the Dockerfiles written that are outlined in the [Instructions to Write API Dockerfile](./Docs/Instructions-To-Write-API-Dockerfile.md) and [Instructions to Write Dashboard Dockerfile](./Docs/Instructions-To-Write-Dashboard-Dockerfile.md).

3. Build and start the Saleor platform by following the deployment steps in [Instructions to Deploy](./Docs/Instructions-To-Deploy-Compose.md).

4. Access the application:
   - **API**: [http://localhost:8000](http://localhost:8000)
   - **Dashboard**: [http://localhost:9000](http://localhost:9000)
   - **Optional Services**:
     - **Jaeger** (APM): [http://localhost:16686](http://localhost:16686)
     - **Mailpit** (Test Email UI): [http://localhost:8025](http://localhost:8025)

---

## Supporting Documentation

### [Instructions to Deploy](Docs/Instructions-To-Deploy-Compose.md)
This guide provides step-by-step instructions for setting up the Saleor platform, including:

- Preparing your environment.
- Building and running the Docker Compose stack.
- Initializing the database and creating an admin user.

### [Instructions to Write API Dockerfile](./Docs/Instructions-To-Write-API-Dockerfile.md)
This guide provides step-by-step instructions for writing the Dockerfile for the Saleor API, including:
- Installing Dependencies
- Single-stage build
- Running the API

### [Instructions to Write Dashboard Dockerfile](./Docs/Instructions-To-Write-Dashboard-Dockerfile.md)
This guide provides step-by-step instructions for writing the Dockerfile for the Saleor Dashboard, including:
- Installing Dependencies
- Multi-stage builds
- Running the Saleor Dashboard

### [Instructions to Build CI/CD Pipelines](./Docs/Instructions-To-Build-CICD-Pipelines.md)
This guide serves as a instructions and requirements for how your CI/CD Pipelines should be built and what outcome they should achieve.

### [Saleor Platform Documentation](Docs/Saleor-Platform-docs.md)
Detailed documentation on:

- Service configurations.
- Customization options.
- Troubleshooting common issues.

### [Saleor Overview](Saleor.md)
Learn more about the Saleor project, including:

- Features and capabilities.
- High-level architecture.
- Use cases and benefits.

---

## Feedback and Contributions

If you encounter issues or have suggestions for improvement, please message me on Slack or through the Google Classroom. Feedback is welcome!

---

Happy deploying! 🚀

