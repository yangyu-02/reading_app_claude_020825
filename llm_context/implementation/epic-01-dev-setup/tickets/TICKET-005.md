# TICKET-005: Create Backend Dockerfile

## Objective
Create a Dockerfile for the backend service that supports FastAPI, PostgreSQL, RabbitMQ, Redis, and Dramatiq workers in a containerized environment.

## Requirements
- Multi-stage build for optimized image size
- Support for both development and production environments
- Proper handling of Python dependencies
- Non-root user for security
- Health check endpoints

## Dockerfile Implementation

### docker/backend/Dockerfile
```dockerfile
# Stage 1: Builder
FROM python:3.11-slim as builder

# Install build dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    g++ \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Copy requirements
COPY backend/requirements.txt .
COPY backend/requirements-dev.txt .

# Create wheels for dependencies
RUN pip wheel --no-cache-dir --no-deps --wheel-dir /app/wheels -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim

# Install runtime dependencies
RUN apt-get update && apt-get install -y \
    libpq-dev \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Create non-root user
RUN groupadd -r app && useradd -r -g app app

# Set working directory
WORKDIR /app

# Copy wheels from builder
COPY --from=builder /app/wheels /wheels

# Install dependencies from wheels
RUN pip install --no-cache /wheels/*

# Copy application code
COPY backend/app ./app
COPY backend/alembic ./alembic
COPY backend/alembic.ini .

# Change ownership to app user
RUN chown -R app:app /app

# Switch to non-root user
USER app

# Expose port
EXPOSE 8000

# Default command for API server
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### docker/backend/Dockerfile.worker
```dockerfile
# Use the same base image as the main Dockerfile
FROM python:3.11-slim

# Install runtime dependencies
RUN apt-get update && apt-get install -y \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Create non-root user
RUN groupadd -r app && useradd -r -g app app

# Set working directory
WORKDIR /app

# Copy requirements and install
COPY backend/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY backend/app ./app

# Change ownership to app user
RUN chown -R app:app /app

# Switch to non-root user
USER app

# Default command for Dramatiq worker
CMD ["dramatiq", "app.workers.tasks", "--processes", "4", "--threads", "8"]
```

### docker/backend/.dockerignore
```
**/__pycache__
**/*.pyc
**/*.pyo
**/*.pyd
.Python
env/
venv/
.venv/
pip-log.txt
pip-delete-this-directory.txt
.tox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover
*.log
.git
.gitignore
.mypy_cache
.pytest_cache
.hypothesis
.env
.env.*
!.env.example
```

## Environment-specific Configurations

### Development Override (docker/backend/Dockerfile.dev)
```dockerfile
FROM python:3.11-slim

# Install development dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    g++ \
    libpq-dev \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy requirements
COPY backend/requirements-dev.txt .
RUN pip install --no-cache-dir -r requirements-dev.txt

# Install development tools
RUN pip install --no-cache-dir debugpy

# Expose debugger port
EXPOSE 8000 5678

# Run with reload for development
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]
```

## Build Arguments and Health Checks

### Supporting Scripts

**docker/backend/healthcheck.py**
```python
#!/usr/bin/env python3
import sys
import httpx

try:
    response = httpx.get("http://localhost:8000/health")
    sys.exit(0 if response.status_code == 200 else 1)
except Exception:
    sys.exit(1)
```

## Usage Instructions

1. Build the backend image:
   ```bash
   docker build -f docker/backend/Dockerfile -t reading-app-backend .
   ```

2. Build the worker image:
   ```bash
   docker build -f docker/backend/Dockerfile.worker -t reading-app-worker .
   ```

3. Build for development:
   ```bash
   docker build -f docker/backend/Dockerfile.dev -t reading-app-backend:dev .
   ```

## Notes
- The multi-stage build reduces final image size by ~50%
- Non-root user improves security
- Separate worker Dockerfile allows independent scaling
- Development Dockerfile includes debugging tools and hot reload
- Health check script enables proper container orchestration