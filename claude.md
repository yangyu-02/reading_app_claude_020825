# Claude Development Workflow

## Overview
This project follows a structured workflow for LLM-assisted development. All development work must follow this process to ensure quality and maintainability.

## Workflow Rules

### 1. Project Structure
All LLM development work happens in the `./llm_context` folder:
- `/llm_context/implementation/roadmap.md` - High-level project roadmap
- `/llm_context/implementation/epic-XX-name/` - Epic folders containing design docs and tickets

### 2. Development Process

#### Phase 1: Epic Design
1. Each major feature starts as an epic with a design document
2. Design docs must be reviewed and approved by humans before proceeding
3. Design docs should include:
   - Goals and scope
   - Technical approach
   - Database/API design if applicable
   - List of potential tickets
   - Open questions for discussion

#### Phase 2: Ticket Creation
1. After design approval, tickets are created as individual markdown files
2. Ticket files follow naming: `TICKET-XXX-description.md`
3. Each ticket should be small and focused (max 200 lines of code)
4. Tickets must include:
   - Clear acceptance criteria
   - Technical approach
   - Files to be created/modified
   - Test requirements

#### Phase 3: Implementation
1. Human and LLM collaborate on ticket implementation details
2. Once approach is agreed upon, LLM writes the code
3. Code must follow project conventions and style guides
4. All code must include appropriate tests

### 3. LLM Guidelines

#### DO:
- Write code only when explicitly asked
- Focus on small, well-defined tasks
- Follow existing code patterns and conventions
- Write tests for all new functionality
- Use type hints in Python code
- Use TypeScript (not JavaScript) for frontend

#### DON'T:
- Create files outside of requested scope
- Make architectural decisions without approval
- Write code without reading relevant existing code first
- Skip error handling or validation
- Add unnecessary comments or documentation

### 4. Code Quality Requirements
- Backend: Must pass ruff and mypy checks
- Frontend: Must pass eslint and prettier checks
- All code must have corresponding tests
- No hardcoded values - use configuration/environment variables

### 5. Communication Protocol
1. Always explain what you're about to do before doing it
2. Ask for clarification when requirements are unclear
3. Suggest alternatives when you see potential issues
4. Confirm understanding of the task before starting implementation

#### Feedback on Design Docs and Tickets
- Human feedback will be provided as inline HTML comments: `<!-- COMMENT: your feedback here -->`
- These comments can appear anywhere in markdown files



## Project-Specific Information

### Technology Stack
- Frontend: React with TypeScript, Vite, Tailwind CSS
- Backend: FastAPI with async SQLAlchemy, Dramatiq
- Database: PostgreSQL with Alembic migrations
- Cache/Queue: Redis, RabbitMQ
- Development: Docker Compose, Makefile



## Current Status
- Current Epic: epic-01-dev-setup
- Phase: Tickets Created (8 tickets ready for implementation)
- See `/llm_context/current_status.md` for detailed project status

## Next Steps
1. Review created tickets in `/llm_context/implementation/epic-01-dev-setup/tickets/`
2. Begin implementing tickets starting with TICKET-001 (monorepo structure)