# TICKET-007: Create Docker Compose Configuration

## Objective
Create a docker-compose.yml file that orchestrates all services for local development: frontend, backend API, PostgreSQL, RabbitMQ, Redis, and Dramatiq workers.

## Requirements
- All services networked together
- Persistent volumes for databases
- Environment variable configuration
- Health checks for service dependencies
- Development-friendly configuration

## Docker Compose Configuration

### docker-compose.yml
```yaml
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:16-alpine
    container_name: reading_app_postgres
    environment:
      POSTGRES_USER: reading_user
      POSTGRES_PASSWORD: reading_pass
      POSTGRES_DB: reading_app
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U reading_user -d reading_app"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - reading_network

  # Redis Cache
  redis:
    image: redis:7-alpine
    container_name: reading_app_redis
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    ports:
      - "6379:6379"
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - reading_network

  # RabbitMQ Message Broker
  rabbitmq:
    image: rabbitmq:3.12-management-alpine
    container_name: reading_app_rabbitmq
    environment:
      RABBITMQ_DEFAULT_USER: rabbit_user
      RABBITMQ_DEFAULT_PASS: rabbit_pass
      RABBITMQ_DEFAULT_VHOST: reading_vhost
    volumes:
      - rabbitmq_data:/var/lib/rabbitmq
    ports:
      - "5672:5672"      # AMQP port
      - "15672:15672"    # Management UI
    healthcheck:
      test: ["CMD", "rabbitmq-diagnostics", "-q", "ping"]
      interval: 30s
      timeout: 10s
      retries: 5
    networks:
      - reading_network

  # Backend API
  backend:
    build:
      context: .
      dockerfile: docker/backend/Dockerfile.dev
    container_name: reading_app_backend
    volumes:
      - ./backend:/app
      - backend_cache:/root/.cache
    environment:
      DATABASE_URL: postgresql+asyncpg://reading_user:reading_pass@postgres:5432/reading_app
      REDIS_URL: redis://redis:6379/0
      RABBITMQ_URL: amqp://rabbit_user:rabbit_pass@rabbitmq:5672/reading_vhost
      SECRET_KEY: dev-secret-key-change-in-production
      ENVIRONMENT: development
      DEBUG: "true"
    ports:
      - "8000:8000"
      - "5678:5678"    # Debugger port
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
      rabbitmq:
        condition: service_healthy
    command: ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]
    networks:
      - reading_network

  # Dramatiq Workers
  worker:
    build:
      context: .
      dockerfile: docker/backend/Dockerfile.worker
    container_name: reading_app_worker
    volumes:
      - ./backend:/app
    environment:
      DATABASE_URL: postgresql+asyncpg://reading_user:reading_pass@postgres:5432/reading_app
      REDIS_URL: redis://redis:6379/0
      RABBITMQ_URL: amqp://rabbit_user:rabbit_pass@rabbitmq:5672/reading_vhost
      SECRET_KEY: dev-secret-key-change-in-production
      ENVIRONMENT: development
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
      rabbitmq:
        condition: service_healthy
      backend:
        condition: service_started
    command: ["dramatiq", "app.workers.tasks", "--processes", "2", "--threads", "4", "--watch", "."]
    networks:
      - reading_network

  # Frontend (optional - can run via pnpm)
  frontend:
    image: node:20-alpine
    container_name: reading_app_frontend
    working_dir: /app
    volumes:
      - ./frontend:/app
      - /app/node_modules
    environment:
      VITE_API_URL: http://localhost:8000
    ports:
      - "5173:5173"
    command: sh -c "npm install -g pnpm && pnpm install && pnpm dev --host"
    networks:
      - reading_network
    profiles:
      - full  # Only runs when --profile full is specified

  # Database migrations (one-off service)
  migrate:
    build:
      context: .
      dockerfile: docker/backend/Dockerfile
    container_name: reading_app_migrate
    environment:
      DATABASE_URL: postgresql+asyncpg://reading_user:reading_pass@postgres:5432/reading_app
    depends_on:
      postgres:
        condition: service_healthy
    command: ["alembic", "upgrade", "head"]
    networks:
      - reading_network
    profiles:
      - migrate

volumes:
  postgres_data:
  redis_data:
  rabbitmq_data:
  backend_cache:

networks:
  reading_network:
    driver: bridge
```

### docker-compose.override.yml (for local development)
```yaml
version: '3.8'

services:
  backend:
    environment:
      PYTHONDONTWRITEBYTECODE: 1
      PYTHONUNBUFFERED: 1
    stdin_open: true
    tty: true

  postgres:
    ports:
      - "5432:5432"
    
  redis:
    ports:
      - "6379:6379"

  rabbitmq:
    ports:
      - "15672:15672"
```

### .env (for docker-compose)
```env
# Database
POSTGRES_USER=reading_user
POSTGRES_PASSWORD=reading_pass
POSTGRES_DB=reading_app

# RabbitMQ
RABBITMQ_DEFAULT_USER=rabbit_user
RABBITMQ_DEFAULT_PASS=rabbit_pass
RABBITMQ_DEFAULT_VHOST=reading_vhost

# Application
SECRET_KEY=dev-secret-key-change-in-production
ENVIRONMENT=development
```

## Usage Commands

### Start all backend services (without frontend)
```bash
docker-compose up -d postgres redis rabbitmq backend worker
```

### Start everything including frontend
```bash
docker-compose --profile full up -d
```

### Run database migrations
```bash
docker-compose --profile migrate run --rm migrate
```

### View logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f backend
```

### Stop all services
```bash
docker-compose down

# Remove volumes too
docker-compose down -v
```

### Access services
- Backend API: http://localhost:8000
- Frontend: http://localhost:5173
- RabbitMQ Management: http://localhost:15672 (user: rabbit_user, pass: rabbit_pass)
- PostgreSQL: localhost:5432
- Redis: localhost:6379

## Notes
- Frontend service is optional (profile: full) since you mentioned preferring pnpm
- Health checks ensure services start in correct order
- Volumes persist data between restarts
- Override file allows easy local customization
- Migrate service runs as one-off for database migrations
- All services are on the same network for inter-service communication