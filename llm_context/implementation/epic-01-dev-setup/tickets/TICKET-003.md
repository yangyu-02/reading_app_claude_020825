# TICKET-003: Propose Backend Dependencies

## Objective
Define the backend technology stack and dependencies for the reading app MVP using FastAPI, SQLAlchemy, and related tools.

## Core Dependencies

### Web Framework & API
- **fastapi**: ^0.109.0 - Modern web framework for building APIs
- **uvicorn[standard]**: ^0.25.0 - ASGI server
- **pydantic**: ^2.5.0 - Data validation using Python type annotations
- **pydantic-settings**: ^2.1.0 - Settings management

### Database & ORM
- **sqlalchemy**: ^2.0.0 - SQL toolkit and ORM
- **alembic**: ^1.13.0 - Database migrations
- **asyncpg**: ^0.29.0 - PostgreSQL async driver
- **psycopg2-binary**: ^2.9.0 - PostgreSQL adapter (fallback)

### Task Queue & Caching
- **dramatiq[redis,rabbitmq]**: ^1.15.0 - Background task processing
- **redis**: ^5.0.0 - Redis client
- **aio-pika**: ^9.3.0 - RabbitMQ async client

### Authentication & Security
- **python-jose[cryptography]**: ^3.3.0 - JWT tokens
- **passlib[bcrypt]**: ^1.7.0 - Password hashing
- **python-multipart**: ^0.0.6 - Form data parsing

### Utilities
- **httpx**: ^0.25.0 - HTTP client
- **python-dotenv**: ^1.0.0 - Environment variables
- **pyyaml**: ^6.0.0 - YAML parsing
- **email-validator**: ^2.1.0 - Email validation

### Development Dependencies
- **pytest**: ^7.4.0 - Testing framework
- **pytest-asyncio**: ^0.21.0 - Async test support
- **pytest-cov**: ^4.1.0 - Coverage reporting
- **ruff**: ^0.1.0 - Linter
- **mypy**: ^1.8.0 - Type checker


## Installation Instructions

1. Navigate to the backend directory:
   ```bash
   cd backend
   ```

2. Create a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Create requirements files:

   **requirements.txt** (production dependencies):
   ```txt
   fastapi==0.109.0
   uvicorn[standard]==0.25.0
   pydantic==2.5.0
   pydantic-settings==2.1.0
   sqlalchemy==2.0.0
   alembic==1.13.0
   asyncpg==0.29.0
   psycopg2-binary==2.9.0
   dramatiq[redis,rabbitmq]==1.15.0
   redis==5.0.0
   aio-pika==9.3.0
   python-jose[cryptography]==3.3.0
   passlib[bcrypt]==1.7.0
   python-multipart==0.0.6
   httpx==0.25.0
   python-dotenv==1.0.0
   pyyaml==6.0.0
   email-validator==2.1.0
   ```

   **requirements-dev.txt** (development dependencies):
   ```txt
   -r requirements.txt
   pytest==7.4.0
   pytest-asyncio==0.21.0
   pytest-cov==4.1.0
   black==23.12.0
   ruff==0.1.0
   mypy==1.8.0
   pre-commit==3.6.0
   ```

4. Install dependencies:
   ```bash
   pip install -r requirements-dev.txt
   ```

5. Create pyproject.toml:
   ```bash
   touch pyproject.toml
   ```

## Configuration Files Needed

### pyproject.toml
```toml
[tool.black]
line-length = 88
target-version = ['py311']

[tool.ruff]
line-length = 88
select = ["E", "F", "I", "N", "UP", "S", "B", "A", "C4", "T20"]
ignore = ["E501"]

[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
ignore_missing_imports = true

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
python_classes = "Test*"
python_functions = "test_*"
asyncio_mode = "auto"
```

### .env.example
```env
DATABASE_URL=postgresql+asyncpg://user:password@localhost/reading_app
REDIS_URL=redis://localhost:6379/0
RABBITMQ_URL=amqp://guest:guest@localhost:5672/
SECRET_KEY=your-secret-key-here
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

## Project Structure
```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py           # FastAPI application
│   ├── config.py         # Settings configuration
│   ├── api/
│   │   ├── __init__.py
│   │   ├── deps.py       # Dependencies
│   │   └── v1/
│   │       ├── __init__.py
│   │       └── endpoints/
│   ├── core/
│   │   ├── __init__.py
│   │   ├── security.py
│   │   └── database.py
│   ├── db/
│   │   ├── __init__.py
│   │   ├── base.py
│   │   └── models/
│   ├── services/
│   │   └── __init__.py
│   └── workers/
│       ├── __init__.py
│       └── tasks.py
├── alembic/
├── tests/
├── requirements.txt
├── requirements-dev.txt
└── pyproject.toml
```

## Notes
- Python 3.11+ is recommended for better performance
- All versions are approximate and should use the latest stable versions
- Dramatiq is chosen over Celery for simpler setup and better performance
- AsyncPG is used for async PostgreSQL operations with SQLAlchemy