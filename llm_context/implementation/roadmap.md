# Reading App Development Roadmap

## Overview
Building a web-based EPUB reader with highlighting, bookmarks, search, and AI-powered Q&A capabilities.

## Phase 1: MVP (Local Development)
- Basic EPUB upload and parsing
- Custom React-based EPUB reader
- Text highlighting functionality
- Adjustable text size
- Bookmarks
- Table of contents navigation
- Search across user's library
- AI Q&A feature for books
- PostgreSQL database with async processing
- Docker-compose setup

## Phase 2: Web Application
- Migration to AWS infrastructure
- S3 integration for file storage
- Kubernetes deployment (EKS)
- Basic authentication (email/password)
- Production-ready database (RDS)
- Enhanced performance and scalability

## Phase 3: SaaS Platform
- Payment integration
- Advanced user management
  - Password reset
  - Email verification
  - Account settings
- Enterprise authentication (WorkOS or similar)
- Monitoring and logging (Sentry)
- Usage analytics
- Subscription management
- Multi-tenant architecture