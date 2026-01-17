```vb
    # Below is a **universal, production-grade `implementation.md`. 
    It is **technology-agnostic** – you can use it for:
    
    * Web apps (frontend + backend)
    * Mobile apps
    * APIs & microservices
    * Frameworks & libraries
    * Desktop tools
    * Automation scripts
    
    The idea is:
    
    👉 **You fill this file first → then you code only what is written here.**
    
    ---
    
    ```md
    # PROJECT IMPLEMENTATION DOCUMENT
    
    ## 1. Project Overview
    
    ### 1.1 Project Name
    - Name:
    - Type: (Web App / Mobile App / API / Library / Framework / CLI / Desktop)
    
    ### 1.2 Problem Statement
    - What problem is being solved?
    - Who experiences this problem?
    - Why current solutions are not enough?
    
    ### 1.3 Goals & Objectives
    - Primary goals:
    - Secondary goals:
    - Non-goals (what this project will NOT do):
    
    ### 1.4 Target Users
    - User personas:
    - Technical level of users:
    - Platforms:
    
    ### 1.5 Success Criteria
    - Functional success:
    - Performance success:
    - Business success:
    
    ---
    
    ## 2. Requirements
    
    ### 2.1 Functional Requirements
    
    #### Core Features
    - Feature 1:
      - Description:
      - Inputs:
      - Outputs:
      - Rules:
      - Errors:
    
    - Feature 2:
      - …
    
    #### User Stories
    - As a [user] I want to [action] so that [benefit]
    
    #### Use Cases
    - UC-01:
      - Actor:
      - Flow:
      - Exceptions:
    
    ---
    
    ### 2.2 Non-Functional Requirements
    
    #### Performance
    - Response time:
    - Throughput:
    - Limits:
    
    #### Security
    - Authentication:
    - Authorization:
    - Data protection:
    
    #### Scalability
    - Expected users:
    - Growth plan:
    
    #### Reliability & Business Continuity
    - Availability (SLA): % Uptime target
    - RTO (Recovery Time Objective): Max allowed downtime
    - RPO (Recovery Point Objective): Max allowed data loss
    - Backup Strategy: (Point-in-time recovery / Cross-region replication)
    - Disaster Recovery Plan: (How to rebuild if a cloud region goes down)
    
    #### Usability
    - Accessibility:
    - UX rules:
    
    ---
    
    ## 3. Technical Architecture
    
    ### 3.1 System Architecture
    
    - Architecture style:
      - Monolith / Microservices / Serverless / MVC / Clean
    
    - High level diagram:
      - Components
      - Data flow
    
    ### 3.2 Technology Stack
    
    #### Frontend
    - Language:
    - Framework:
    - Libraries:
    
    #### Backend
    - Language:
    - Framework:
    - ORM:
    
    #### Database
    - Type:
    - Schema:
    - Migration Tool: (Prisma / Flyway / Liquibase)
    
    ### 3.4 Third-Party Services
    - External APIs: (Stripe / OpenAI / Twilio)
    - Auth Provider: (Auth0 / Clerk / Firebase Auth)
    - Webhooks: (Source, Security/Signing Secrets)
    
    ### 3.5 Compliance & Privacy
    - Data Retention: (How long we keep logs/user data)
    - PII Handling: (Encryption at rest / Masking)
    - Compliance: (GDPR / HIPAA / SOC2 requirements)
    
    #### DevOps & Infrastructure
    - Hosting:
    - Infrastructure as Code: (Terraform / Pulumi / CloudFormation)
    - CI/CD:
    - Secrets Management: (Vault / AWS Secrets Manager)
    
    #### Observability
    - Logging: (Structured JSON / ELK / Axiom)
    - Error Tracking: (Sentry / GlitchTip)
    - Metrics & Tracing: (Prometheus / OpenTelemetry)
    
    ---
    
    ### 3.3 Folder Structure
    
    ```
    
    project/
    │
    ├── src/
    ├── tests/
    ├── docs/
    ├── scripts/
    └── config/
    
    ```
    
    ---
    
    ## 4. Data Design
    
    ### 4.1 Domain Model
    
    #### Entities
    
    - Entity: User
      - id:
      - name:
      - rules:
    
    - Entity: X
    
    ### 4.2 Database Schema
    
    - Tables
    - Relationships
    - Indexes
    
    ### 4.3 API Contracts
    
    #### Endpoint: POST /api/x
    - Request:
    - Response:
    - Errors:
    
    ---
    
    ## 5. UI/UX Design (If Applicable)
    
    ### 5.1 Screens
    
    - Screen 1
      - Purpose:
      - Components:
      - Validations:
    
    ### 5.2 Navigation Flow
    
    - User journey
    
    ### 5.3 Design Rules
    
    - Colors:
    - Typography:
    - Accessibility:
    
    ---
    
    ## 6. Security Design
    
    ### 6.1 Authentication
    - Method:
    - Tokens:
    - Expiry:
    
    ### 6.2 Authorization
    - Roles:
    - Permissions:
    
    ### 6.3 Data Security
    - Encryption:
    - Validation:
    - Sanitization:
    
    ---
    
    ## 7. Implementation Plan (PHASED)
    
    THIS IS THE MOST IMPORTANT SECTION  
    👉 You will code EXACTLY in this order.

    ---
    ### PHASE 0 – Prerequisites & Identity

    1.  **Identity & Branding**
        - [ ] Domain name secured
        - [ ] Brand assets (Logo, Favicon, Brand Colors)
        - [ ] Social media handles / NPM organization name reserved
    2.  **Legal & Compliance**
        - [ ] License selected (MIT, Apache 2.0, Proprietary)
        - [ ] Terms of Service & Privacy Policy drafts (if public-facing)
        - [ ] Contributor License Agreement (CLA) (if open-source)
    3.  **Access Management**
        - [ ] Cloud provider account (AWS/GCP/Azure) created
        - [ ] Third-party service accounts (Stripe, Twilio, OpenAI) active
        - [ ] Team members invited to GitHub/GitLab and Slack/Discord
    4.  **Security Baseline**
        - [ ] Password Manager / Team Vault initialized (1Password/Bitwarden)
        - [ ] Multi-Factor Authentication (MFA) enabled on all root accounts
    
    ---
    
    ### PHASE 1 – Project Setup

    1. Create repository  
    2. Initialize project  
    3. Setup:
       - Linter & Formatter
       - .gitignore & .dockerignore (Ensure secrets are excluded)
       - Pre-commit hooks (Secret scanning with 'gitleaks' or 'talisman')
       - Environment variables template (`.env.example`) 
    
    DELIVERABLE:
    - Running empty project
    
    ---
    
    ### PHASE 2 – Core Foundation
    
    1. Architecture skeleton  
    2. Base modules  
    3. Config system  
    4. Error handling  
    
    TEST:
    - App starts  
    - Logger works  
    
    ---
    
    ### PHASE 3 – Domain Layer
    
    1. Models  
    2. Business rules  
    3. Validations  
    
    TEST:
    - Unit tests for domain
    
    ---
    
    ### PHASE 4 – Data Layer
    
    1. Database  
    2. Repositories  
    3. Migrations  
    
    TEST:
    - CRUD tests
    
    ---
    
    ### PHASE 5 – Service Layer
    
    1. Business services  
    2. Logic  
    3. Integrations  
    
    TEST:
    - Service tests
    
    ---
    
    ### PHASE 6 – Interface Layer
    
    1. API / UI  
    2. Controllers  
    3. Routes  
    
    TEST:
    - API tests
    
    ---
    
    ### PHASE 7 – Frontend (if any)
    
    1. Layout  
    2. Components  
    3. State  
    4. API integration  
    
    ---
    
    ### PHASE 8 – Security
    
    1. Auth  
    2. Permissions  
    3. Validation  
    
    ---
    
    ### PHASE 9 – Testing
    
    #### Unit Tests
    - What to test:
    
    #### Integration Tests
    
    #### E2E Tests
    
    ---
    
    ### PHASE 10 – DevOps & Observability

    1. Infrastructure Provisioning (IaC)
    2. Docker & Containerization
    3. CI/CD Pipelines
    4. Error Tracking & Centralized Logging setup
    5. Alerting rules (What triggers a page/notification?) 
    
    ---
    
    ### PHASE 11 – Documentation
    
    1. README  
    2. API docs  
    3. User guide  
    
    ---
    
    ### PHASE 12 – Release
    
    1. Versioning  
    2. Changelog  
    3. Deployment  
    
    ---
    
    ## 8. Coding Standards
    
    ### 8.1 Principles
    - SOLID  
    - DRY  
    - KISS  
    
    ### 8.2 Style Guide
    - Naming  
    - Structure  
    
    ### 8.3 Git Rules
    - Branching  
    - Commits  
    
    ---
    
    ## 9. Testing Strategy
    
    ### 9.1 Test Pyramid
    - Unit  
    - Integration  
    - E2E  
    
    ### 9.2 Test Cases
    
    - TC-01:
      - Given:
      - When:
      - Then:
    
    ---
    
    ## 10. Deployment Plan
    
    - Environments  
    - Steps  
    - Rollback  
    
    ---
    
    ## 11. Maintenance & Operations

    - Dashboard Links: (Link to Grafana/Datadog)
    - Runbooks: (Step-by-step guides for common failures)
    - Dependency Updates: (Schedule for patching libraries)
    - Log Rotation: (Retention and archival policy)  
    
    ---
    
    ## 12. Future Improvements
    
    - Ideas  
    - Tech debt  
    
    ---
    
    ## 13. Checklist BEFORE CODING

    - [ ] **Phase 0 Complete:** Identity, Legal, and Access secured
    - [ ] **Requirements:** Business and Functional goals finalized
    - [ ] **Architecture:** Tech stack and System Design approved
    - [ ] **Data:** Models defined and API contracts signed-off
    - [ ] **Infrastructure:** CI/CD and Observability strategy ready
    - [ ] **Security:** Secret management and .gitignore validated  
    
    👉 ONLY AFTER THIS – START CODING PHASE 1
    ```
    
    ---
    
    # HOW YOU SHOULD USE THIS (IMPORTANT)
    
    ### WORKFLOW
    
    1. Create project folder
    2. Create `implementation.md`
    3. FILL THIS FILE FIRST
    4. Then:
    
    👉 Open laptop
    👉 Open this file
    👉 Implement ONLY phase by phase
    
    NEVER jump to code without updating:
    
    * Requirements
    * Models
    * API
    * Tests
    ---
```



