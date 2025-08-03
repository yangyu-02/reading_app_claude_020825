# Epic 01: MVP Foundation Design Document

## Epic Overview
Build the foundational infrastructure for the reading app MVP, including project setup.

## Goals
1. Set up monorepo structure with frontend and backend
2. Install dependencies for frontend and backend
3. Configure local development environment with Docker Compose

## Technical Design

### 1. Project Structure
```
reading-app/
├── frontend/               # React + TypeScript + Vite
├── backend/                # FastAPI + SQLAlchemy
├── docker/                 # Docker configurations
├── bin/                    # Development scripts
├── docker-compose.yml      # Local development setup
├── .github/                # GitHub Actions CI/CD
└── llm_context/           # LLM development workflow
```


## Potential Tickets
1. **TICKET-001**: propose a monorepo structure
2. **TICKET-002**: propose a list of dependencies for frontend. give instructions, but do not install them
3. **TICKET-003**: propose a list of dependencies for backend. give instructions, but do not install them
5. **TICKET-005**: create a Dockerfile for backend, this means backend service, postgres, rabbitmq, redis, and separate worker services via dramatiq.
6. **TICKET-006**: for frontend, we don't need docker, i can spin it up via pnpm but we need to set up the commands
7. **TICKET-007**: create a docker-compose.yml file that includes all services: frontend, backend, postgres, rabbitmq, redis, and dramatiq workers
8. **TICKET-008**: create a makefile for easier development commands
9. **TICKET-009**: create a summary of the epic, things we've done, create current_status.md file in the root llm_context folder and update it. this file should show the current state the app is in. we should then update claude.md to reflect this workflow.
