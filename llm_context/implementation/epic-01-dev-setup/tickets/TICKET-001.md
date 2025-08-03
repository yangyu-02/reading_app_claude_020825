# TICKET-001: Propose Monorepo Structure

## Objective
Design and propose a monorepo structure for the reading app MVP that supports both frontend and backend development.

## Requirements
- Clear separation between frontend and backend code
- Support for shared configurations and types
- Scalable structure for future features
- Development tooling and script organization

## Proposed Structure
```
reading-app/
├── frontend/              # React + TypeScript + Vite application
│   ├── src/               # Source code
│   ├── public/            # Static assets
│   ├── package.json       # Frontend dependencies
│   └── vite.config.ts     # Vite configuration
│
├── backend/               # FastAPI + SQLAlchemy application
│   ├── app/              # Application code
│   │   ├── api/          # API endpoints
│   │   ├── core/         # Core functionality [what is in core?]
│   │   ├── db/           # Database models and migrations
│   │   ├── services/     # Business logic
│   │   └── workers/      # Background task workers
│   ├── tests/            # Backend tests
│   ├── requirements.txt  # Python dependencies [you don't need this, uv will create it]
│   └── pyproject.toml    # Python project configuration [same you don't need this]
│
├── docker/               # Docker configurations
│   ├── backend/         # Backend Dockerfile
│   
│
├── bin/                 # Development scripts
│   ├── setup.sh        # Initial setup script <notes>
│   └── dev.sh          # Development helper scripts [like what?]
│
├── .github/             # GitHub Actions CI/CD
│   └── workflows/      # CI/CD pipelines
│
├── llm_context/         # LLM development workflow
│   ├── claude.md       # Claude context and guidelines
│   ├── current_status.md # Current project status
│   └── implementation/ # Implementation details
│
├── docker-compose.yml   # Local development orchestration
├── Makefile            # Development commands
├── .gitignore          # Git ignore rules
└── README.md           # Project documentation
```

## Benefits
1. **Clear Separation**: Frontend and backend are isolated with their own dependencies
2. **Shared Resources**: Common configurations can be placed at root level
3. **Docker Support**: Dedicated docker directory for container configurations
4. **Development Tools**: bin/ directory for scripts, Makefile for common commands
5. **LLM Workflow**: Dedicated context directory for AI-assisted development
6. **Scalability**: Structure allows for easy addition of new services or features

## Implementation Notes
- Frontend uses pnpm for package management
- Backend uses uv for Python dependencies
- Docker Compose orchestrates local development environment
- Makefile provides shortcuts for common development tasks

