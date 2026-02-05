# Copilot Instructions - Splitwise-Lite

This repository contains the Splitwise-Lite expense-sharing application. This file provides quick navigation to all AI agent guidance documents.

## Documentation Structure

### **Start Here: Project Context**

#### [**Technical Context**](../context/technical-context.md)
**Purpose**: Complete system architecture, data flows, and technical decisions  
**What it covers**:
- System overview and architecture (Java/Spring Boot backend + React frontend)
- MongoDB data model and persistence strategy
- API endpoint documentation
- Docker Compose deployment
- Intentional legacy patterns (for learning)
- Development workflow and debugging

**When to use**: 
- First time working in this codebase
- Understanding how backend and frontend interact
- Debugging issues
- Making architectural decisions
- Understanding the workshop learning objectives

#### [**Business Domain Context**](../context/business-domain-context.md)
**Purpose**: Domain concepts, business logic, and expense-sharing workflows  
**What it covers**:
- Core domain concepts (Groups, Members, Expenses, Splits)
- Expense calculation algorithms
- Balance and settlement logic
- Business rules and constraints

**When to use**:
- Understanding domain concepts
- Implementing new features
- Validating business logic
- Understanding settlement algorithm

---

### **Coding Standards & Guidelines**

#### [**Core Standards**](./guidelines/core-standards.md) ⭐ READ FIRST
**Purpose**: Universal coding principles applicable to ALL languages and projects  
# Copilot Instructions - Kimai Bulk Submit Helper

This repository contains the **Kimai Bulk Submit Helper** application – a separate helper web app that sits on top of Technogise's Kimai instance and automates bulk timesheet filling and submission. This file provides quick navigation to all AI agent guidance documents and system-design artifacts.

## Documentation Structure

### **Start Here: System Design**

#### [**Kimai Bulk Submit Helper – Problem & Goals**](./stories-and-plans/system-designs/kimai-bulk-submit-helper/01-problem-and-goals.md)
**Purpose**: High-level product context and why this app exists.  
**What it covers**:
- Problem statement and pain points with manual Kimai usage.
- Target users (Technogise employees) and usage context.
- Core user journeys (first-time connection, returning user, month view, template, review & bulk submit).
- Goals and explicit non-goals for V1.

**When to use**:
- First time working in this codebase.
- When deciding whether a feature belongs in this helper vs in Kimai itself.
- To validate that new ideas align with the original problem and scope.

#### [**Requirements & NFRs**](./stories-and-plans/system-designs/kimai-bulk-submit-helper/02-requirements-and-nfrs.md)
**Purpose**: Functional requirements plus non-functional expectations.  
**What it covers**:
- Core features and flows the helper must support.
- Availability, performance, scalability and data-retention assumptions.
- Security and privacy expectations around Kimai API tokens.

**When to use**:
- Before adding sizeable new features or flows.
- When making trade-offs that impact performance, UX or security.

#### [**Architecture & Tech Stack**](./stories-and-plans/system-designs/kimai-bulk-submit-helper/03-architecture-and-tech-stack.md)
**Purpose**: High-level system architecture and chosen technologies.  
**What it covers**:
- Overall architecture (pure frontend React SPA talking directly to Kimai APIs).
- How the app talks to Kimai and where data lives.
- Chosen tech stack and key design decisions.

**When to use**:
- When introducing new modules, libraries or infrastructure.
- To ensure implementation stays consistent with the agreed architecture.

#### [**Components & Responsibilities**](./stories-and-plans/system-designs/kimai-bulk-submit-helper/04-components-and-responsibilities.md)
**Purpose**: Logical breakdown of the helper into components/services.  
**What it covers**:
- Major frontend components / logical services and what each is responsible for.
- How components interact (e.g., token handling, month view, template management, submission flow).

**When to use**:
- Before refactoring or splitting components.
- When deciding where new behavior should live.

#### [**Data Model & Schemas**](./stories-and-plans/system-designs/kimai-bulk-submit-helper/05-data-model-and-schemas.md)
**Purpose**: Conceptual data model used by the helper and how it maps to Kimai.  
**What it covers**:
- Core entities (days, templates, entries) and their relationships.
- How Kimai’s own entities and APIs map to the helper’s internal model.

**When to use**:
- Designing new state structures or local storage.
- Reasoning about how changes affect Kimai data.

#### [**API Design**](./stories-and-plans/system-designs/kimai-bulk-submit-helper/06-api-design.md)
**Purpose**: External API usage and any helper-internal API contracts.  
**What it covers**:
- Kimai API endpoints the helper calls and expected contracts.
- Any internal API boundaries you may introduce later (if a backend is added).

**When to use**:
- Integrating new Kimai endpoints.
- Evolving or introducing any backend or shared API layer.

#### [**Deployment, Operations & Roadmap**](./stories-and-plans/system-designs/kimai-bulk-submit-helper/07-deployment-operations-and-roadmap.md)
**Purpose**: How the helper is deployed, operated and evolved.  
**What it covers**:
- Deployment model and hosting assumptions.
- Operational concerns (monitoring, error handling at a system level).
- Implementation roadmap (MVP → scaling/hardening → advanced features) and known risks.

**When to use**:
- Planning phases of work or sequencing features.
- Discussing deployment changes or reliability improvements.

> **Rule for all structural / architectural changes**: **Update the relevant system-design markdown first**, then implement the code changes so that documentation and implementation stay in sync.

---

### **Coding Standards & Guidelines**

These guidelines are shared across projects and apply here as well.

#### [**Core Standards**](./guidelines/core-standards.md) ⭐ READ FIRST
**Purpose**: Universal coding principles applicable to ALL languages and projects.  
**What it covers**:
- General principles (KISS, YAGNI, DRY, Boy Scout Rule, SOLID).
- Code smell detection and elimination.
- Method/class size guidelines.
- Naming conventions and readability requirements.
- Cross-functional requirements (error handling, logging, security, performance, i18n).
- Testing best practices.

**When to consider**: 
- **Before writing ANY code** – these are mandatory principles.
- When reviewing code quality.
- Resolving design decisions.
- Identifying code smells.
- Refactoring legacy code.

#### [**Java & Spring Boot Standards**](./guidelines/java-spring-boot.md)
**Purpose**: Java 17 and Spring Boot 3.2 best practices for any backend services you might introduce.  
**What it covers**:
- Java language standards (modern Java features, type system, null safety).
- Spring Boot patterns (dependency injection, component annotations).
- Layered architecture (Controller → Service → Repository).
- MongoDB with Spring Data.
- DTOs and validation (Bean Validation).
- Exception handling and global error handling.
- Transaction management.
- Testing (JUnit 5, Mockito, Testcontainers).
- Logging and configuration.

**When to use**:
- If you later add a backend component (e.g., proxying Kimai, adding auth, or multi-tenant features).
- Writing Spring Boot controllers, services or repositories.
- Working with MongoDB entities.
- Implementing REST API endpoints.
- Writing backend tests.

#### [**React & JavaScript Standards**](./guidelines/react-javascript.md)
**Purpose**: React 18.2 and modern JavaScript best practices for the Kimai Bulk Submit Helper frontend.  
**What it covers**:
- Modern JavaScript (ES6+): const/let, arrow functions, destructuring, async/await.
- React functional components and hooks.
- State management (useState, useEffect, Context API).
- Component design patterns (presentational vs container).
- API integration with Axios/fetch.
- React Router v6 for routing.
- Forms and validation.
- Error handling (Error Boundaries).
- Testing (React Testing Library, Jest).
- Performance and accessibility.

**When to use**:
- Working on the React SPA that talks to Kimai.
- Managing state and side effects.
- Integrating with Kimai APIs.
- Writing frontend tests.

---

## Repository Structure

Current high-level structure:

```
kimai/
├── .github/
│   ├── agents/                 # Copilot agent configs (plan/review/system-design modes)
│   ├── guidelines/             # Shared coding standards
│   └── stories-and-plans/
│       ├── implementation-plans/  # Stepwise implementation plans (if present)
│       └── system-designs/        # System design docs (e.g. kimai-bulk-submit-helper)
└── README.md                   # High-level project overview
```

As the implementation grows (e.g. `frontend/`, `backend/`), keep new folders consistent with the patterns recommended in the guidelines and system-design documents.

---

## Recommended Workflow

### For Any Significant Change or New Feature
1. **Understand the product**: Start with the system-design docs – especially:
	- Problem & Goals.
	- Requirements & NFRs.
	- Architecture & Tech Stack.
2. **Align or extend the design**:
	- If your change affects flows, architecture, or data model, first update the relevant system-design markdown under `system-designs/kimai-bulk-submit-helper/`.
3. **Plan the work**:
	- Optionally add or update an implementation plan under `stories-and-plans/implementation-plans/`.
4. **Implement**:
	- For frontend work: follow the React & JavaScript standards.
	- For any backend work (if introduced): follow the Java & Spring Boot standards.
5. **Test**:
	- Add/extend unit tests and integration tests where relevant.
	- For frontend, prefer React Testing Library/Jest; for backend, JUnit/Mockito/Testcontainers.

### For Frontend Development (React SPA)
1. **Before coding**: Review `core-standards.md` and relevant sections in the system design (architecture, components, data model).
2. **While coding**: Follow `react-javascript.md` for component design, state management and API integration with Kimai.
3. **Always**: Keep UX aligned with the flows described in the system design docs.

### For Backend or Infra Extensions (If Added Later)
1. Confirm the need in the system-design docs and extend them first (architecture, components, data model, deployment).
2. Follow `java-spring-boot.md` and `core-standards.md`.

---

## Quick Reference by Task

| Task | Primary References |
|------|--------------------|
| Clarify product scope or flows | System design `01-problem-and-goals.md`, `02-requirements-and-nfrs.md` |
| Change month view / template behavior | System design `03-architecture-and-tech-stack.md`, `04-components-and-responsibilities.md` + React guidelines |
| Adjust local data/state structures | System design `05-data-model-and-schemas.md` + Core standards |
| Integrate new Kimai endpoints | System design `06-api-design.md` + React/JavaScript standards |
| Plan deployment or reliability improvements | System design `07-deployment-operations-and-roadmap.md` |
| General code quality & refactors | `core-standards.md` |
| React component, forms, API integration | `react-javascript.md` |
| Backend services (if added) | `java-spring-boot.md` |

---

## Code Review Checklist

Before submitting code for review on this project:

- [ ] **Design Alignment**: Changes are consistent with the Kimai Bulk Submit Helper system-design docs (and those docs are updated where needed).
- [ ] **Core Standards**: Applied SOLID principles, avoided code smells, kept methods/classes small and focused.
- [ ] **Types & Contracts**: Clear data shapes (frontend) and well-defined contracts to Kimai APIs.
- [ ] **Error Handling**: Graceful handling of Kimai errors, network failures and invalid tokens; proper user-facing feedback.
- [ ] **Security & Privacy**: No hardcoded secrets; Kimai API tokens are handled as per design (browser-only unless explicitly changed in design docs).
- [ ] **Testing**: Appropriate unit/component tests added or updated.
- [ ] **Logging & Observability** (if backend or infra exists): Meaningful, non-sensitive logs and basic monitoring hooks.
- [ ] **Documentation**: Any non-trivial behavior or trade-offs are reflected in the relevant system-design markdown.
---

## Additional Resources

- [React Documentation](https://react.dev/)
- [React Router](https://reactrouter.com/)
- [Spring Boot Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/) – if a backend is added
- [Spring Data MongoDB](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/) – if MongoDB is used
