I want to build a reading app similar to certain amazon cloud readers, where user can upload a document in the epub format and read it in the browser, it should have basic features like, highlighting, text size adjustment, bookmarks, search (across the user's library) ,and table of contents. I also want to be able to add a feature where user can ask the book questions.

I want to build this app in 3 steps:
1. MVP: I should be able to run this locally. We don't need auth, we don't need to worry about file systems. But I do want async processing and database usage from day 1.
2. Web app: at this step I want to migrate to using S3 for file processing, and I want to deploy my app on AWS using k8s. I want basic auth like email and password.
3. Full SaaS app where I can charge money for it. This means I need to integrate payments, user management like password reset, email verification, and a more robust authn/authz system, possibly integrate with workOS or similar service. I also need monitoring and logging, probably using something like sentry.

Tech stack:
- monorepo
- frontend: React,TypeScript,Vite,Tailwind CSS,react-router
- backend: python, FastAPI, SQLAlchemy, pydantic
- database: PostgreSQL
- search engine: PostgreSQL full-text search
- async processing: Dramatiq and rabbitmq. use async processing from day 1
- storage: file system to start, then S3
- cache: Redis
- AI: use anthropic api
- CI/CD: GitHub Actions
- dev tools: ruff, mypy for backend, eslint, prettier for frontend
- local development: docker-compose
- deployment: AWS EKS for k8s, RDS for PostgreSQL, S3 for storage
- Frontend EPUB Rendering: custom solution, i.e. build our own epub reader using React components
- testing: vitest for frontend, pytest for backend. focus on unit test and not a lot of integration tests, minimal e2e tests


# llm development
- I want to limit the usage of llms to only write code on small tasks and only when i ask it to.
- to that end, create a ./llm_context folder, and put a /implementation folder inside it.
- in it, we should have roadmap.md that provides a high level plan on what we want to do, no technical deatails here
- then we should create several folders, each folder is a epic, and each epic should have a design doc where humans and llms can collaborate on the design of the feature. the outcome of design doc is a list of tickets.
- each ticket will then be turn into a md file in the epic folder, humans and llms can collaborate on the implementation of the ticket. Once we agree on the implementation, it'll be handed off to the llm to write the code.
- create a claude.md file that forces this workflow.

The outcome of this first promprt should be that:
- the first epic is created, and the design doc is created. I will then go check out the design doc, provide feedback, and then once I ask for it, create some tickets.
