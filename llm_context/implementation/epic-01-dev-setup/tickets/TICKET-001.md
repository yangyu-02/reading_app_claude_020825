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
├── bin/                 # Development scripts
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

## Implementation Notes
- Just create the folders

Notes:
- look at the overall implementation plan for this epic. This first ticket does NOT need to create any thing like fill out the docker compose or create the docker files. it's just setting up the project structure.
- do not install anything, that comes next, don't even create package.json or requirements.txt files, just the folders.