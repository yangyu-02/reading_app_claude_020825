# TICKET-008: Create Makefile for Development Commands

## Objective
Create a Makefile with convenient commands for common development tasks, making it easier to manage the monorepo and its services.

## Requirements
- Commands for starting/stopping services
- Database management commands
- Development workflow shortcuts
- Testing and linting commands
- Build and deployment helpers

## Makefile Implementation

### Makefile
```makefile
.PHONY: help
help: ## Show this help message
	@echo 'Usage: make [target]'
	@echo ''
	@echo 'Targets:'
	@awk 'BEGIN {FS = ":.*?## "} /^[a-zA-Z_-]+:.*?## / {printf "  %-20s %s\n", $$1, $$2}' $(MAKEFILE_LIST)

# ---------- Setup Commands ----------
.PHONY: install
install: ## Install all dependencies (frontend and backend)
	cd frontend && pnpm install
	cd backend && pip install -r requirements-dev.txt

.PHONY: setup
setup: ## Initial project setup
	@echo "Setting up reading app..."
	@make install
	@make docker-build
	@make db-create
	@echo "Setup complete! Run 'make dev' to start development."

# ---------- Docker Commands ----------
.PHONY: docker-build
docker-build: ## Build all Docker images
	docker-compose build

.PHONY: docker-up
docker-up: ## Start all backend services (postgres, redis, rabbitmq, backend, worker)
	docker-compose up -d postgres redis rabbitmq backend worker

.PHONY: docker-down
docker-down: ## Stop all services
	docker-compose down

.PHONY: docker-clean
docker-clean: ## Stop services and remove volumes
	docker-compose down -v

.PHONY: docker-logs
docker-logs: ## Follow logs for all services
	docker-compose logs -f

.PHONY: docker-logs-backend
docker-logs-backend: ## Follow logs for backend service
	docker-compose logs -f backend

.PHONY: docker-ps
docker-ps: ## Show running containers
	docker-compose ps

# ---------- Development Commands ----------
.PHONY: dev
dev: ## Start development environment (backend services + frontend)
	@make docker-up
	@echo "Backend services started. Starting frontend..."
	cd frontend && pnpm dev

.PHONY: dev-backend
dev-backend: docker-up ## Start only backend services

.PHONY: dev-frontend
dev-frontend: ## Start only frontend dev server
	cd frontend && pnpm dev

.PHONY: dev-stop
dev-stop: docker-down ## Stop all development services

# ---------- Database Commands ----------
.PHONY: db-create
db-create: ## Create database and run migrations
	docker-compose up -d postgres
	@sleep 3  # Wait for postgres to be ready
	docker-compose --profile migrate run --rm migrate

.PHONY: db-migrate
db-migrate: ## Run database migrations
	docker-compose --profile migrate run --rm migrate

.PHONY: db-makemigrations
db-makemigrations: ## Create new migration
	docker-compose run --rm backend alembic revision --autogenerate -m "$(msg)"

.PHONY: db-downgrade
db-downgrade: ## Downgrade database by one revision
	docker-compose run --rm backend alembic downgrade -1

.PHONY: db-reset
db-reset: ## Reset database (WARNING: destroys all data)
	@echo "WARNING: This will destroy all data. Press Ctrl+C to cancel."
	@sleep 3
	docker-compose down -v postgres
	@make db-create

.PHONY: db-shell
db-shell: ## Open PostgreSQL shell
	docker-compose exec postgres psql -U reading_user -d reading_app

# ---------- Backend Commands ----------
.PHONY: backend-shell
backend-shell: ## Open bash shell in backend container
	docker-compose exec backend /bin/bash

.PHONY: backend-test
backend-test: ## Run backend tests
	docker-compose run --rm backend pytest

.PHONY: backend-test-cov
backend-test-cov: ## Run backend tests with coverage
	docker-compose run --rm backend pytest --cov=app --cov-report=html

.PHONY: backend-lint
backend-lint: ## Run backend linting
	cd backend && ruff check .

.PHONY: backend-format
backend-format: ## Format backend code
	cd backend && ruff format .

.PHONY: backend-typecheck
backend-typecheck: ## Run backend type checking
	cd backend && mypy app

# ---------- Frontend Commands ----------
.PHONY: frontend-build
frontend-build: ## Build frontend for production
	cd frontend && pnpm build

.PHONY: frontend-preview
frontend-preview: frontend-build ## Preview production build
	cd frontend && pnpm preview

.PHONY: frontend-test
frontend-test: ## Run frontend tests
	cd frontend && pnpm test

.PHONY: frontend-lint
frontend-lint: ## Run frontend linting
	cd frontend && pnpm lint

.PHONY: frontend-format
frontend-format: ## Format frontend code
	cd frontend && pnpm format

.PHONY: frontend-typecheck
frontend-typecheck: ## Run frontend type checking
	cd frontend && pnpm typecheck

# ---------- Quality Commands ----------
.PHONY: lint
lint: backend-lint frontend-lint ## Run all linting

.PHONY: format
format: backend-format frontend-format ## Format all code

.PHONY: typecheck
typecheck: backend-typecheck frontend-typecheck ## Run all type checking

.PHONY: test
test: backend-test frontend-test ## Run all tests

.PHONY: check
check: lint typecheck test ## Run all checks (lint, typecheck, test)

# ---------- Utility Commands ----------
.PHONY: clean
clean: ## Clean build artifacts and caches
	find . -type d -name "__pycache__" -exec rm -rf {} +
	find . -type f -name "*.pyc" -delete
	rm -rf frontend/dist
	rm -rf backend/.pytest_cache
	rm -rf backend/htmlcov
	rm -rf .coverage

.PHONY: rabbitmq-ui
rabbitmq-ui: ## Open RabbitMQ management UI
	@echo "Opening RabbitMQ Management UI..."
	@echo "URL: http://localhost:15672"
	@echo "Username: rabbit_user"
	@echo "Password: rabbit_pass"

.PHONY: api-docs
api-docs: ## Open API documentation
	@echo "Opening API documentation..."
	@echo "URL: http://localhost:8000/docs"

# ---------- Git Commands ----------
.PHONY: pre-commit
pre-commit: ## Run pre-commit checks
	@make format
	@make check

# Default target
.DEFAULT_GOAL := help
```

## Usage Examples

### Initial Setup
```bash
# First time setup
make setup

# Start development
make dev
```

### Daily Development
```bash
# Start everything
make dev

# Start only backend
make dev-backend

# View logs
make docker-logs

# Run tests
make test

# Format code
make format
```

### Database Operations
```bash
# Create new migration
make db-makemigrations msg="Add user profile"

# Apply migrations
make db-migrate

# Access database shell
make db-shell

# Reset database (careful!)
make db-reset
```

### Code Quality
```bash
# Run all checks before committing
make check

# Or use pre-commit helper
make pre-commit
```

### Troubleshooting
```bash
# Check running services
make docker-ps

# Access backend shell
make backend-shell

# Clean everything
make clean
make docker-clean
```

## Notes
- The help target shows all available commands with descriptions
- Commands are organized into logical groups
- Common workflows have shortcuts (e.g., `make dev` starts everything)
- Dangerous commands (like `db-reset`) have warnings
- The Makefile assumes the project structure from TICKET-001