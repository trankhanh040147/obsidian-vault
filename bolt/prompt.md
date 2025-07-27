Help me write a PROGRESS.md to make plan for develop a frontend web for this project: mongo-index-manager

- Always read and update PROGRESS.md
- Always follow system prompt from .bolt/prompt
- Always follow API Document at: openai.yaml 
---
As a Senior Frontend Architect and Technical Project Manager, you are tasked with creating a comprehensive development plan for a new web-based frontend for an existing backend project named `mongo-index-manager`.

The `mongo-index-manager` is a backend service that provides a REST API to connect to MongoDB instances, list databases and collections, and manage their indexes (e.g., create, view, delete). The frontend should provide a user-friendly, graphical interface for these operations.

Generate the complete content for a `PROGRESS.md` file that will serve as the technical roadmap for the frontend development team. The document must be well-structured, detailed, and written in professional Markdown format.

The `PROGRESS.md` file must include the following sections:

1.  **Project Vision & Goals:**
    *   A concise statement defining the purpose of the frontend application.
    *   A bulleted list of specific, measurable, achievable, relevant, and time-bound (SMART) goals for the project (e.g., "Reduce time for index management by 50% for DBAs," "Achieve a Lighthouse performance score of 90+").

2.  **Technical Stack Proposal:**
    *   **Framework:** Recommend a modern JavaScript framework (e.g., React with Vite, Next.js, or Vue.js) and provide a brief justification for the choice.
    *   **UI Component Library:** Suggest a suitable library (e.g., Material-UI, Ant Design, Shadcn/ui) and explain why it fits the project's needs for building data-dense interfaces.
    *   **State Management:** Propose a robust state management solution (e.g., Redux Toolkit, Zustand, Pinia).
    *   **Styling:** Recommend a styling strategy (e.g., CSS-in-JS, Tailwind CSS).
    *   **Testing:** Outline a comprehensive testing strategy, specifying tools for unit (Jest/Vitest), component (React Testing Library), and end-to-end (Cypress/Playwright) testing.

3.  **Core Features (Epics):**
    *   Break down the required functionality into high-level epics. At a minimum, include:
        *   Epic 1: Connection Management (CRUD for MongoDB connection strings).
        *   Epic 2: Database Explorer (Navigate databases and collections).
        *   Epic 3: Index Management (List, view details, create, and delete indexes).
        *   Epic 4: User Interface & Experience (Dashboard, notifications, error handling).

4.  **Assumed Backend API Contract:**
    *   Define the primary REST API endpoints the frontend will need to consume. Assume a standard RESTful design. Provide examples for key operations:
        *   `POST /api/v1/connections`
        *   `GET /api/v1/connections`
        *   `GET /api/v1/connections/{connId}/databases`
        *   `GET /api/v1/databases/{dbName}/collections`
        *   `GET /api/v1/collections/{collName}/indexes`
        *   `POST /api/v1/collections/{collName}/indexes` (with a sample request body)
        *   `DELETE /api/v1/indexes/{indexName}`

5.  **Development Milestones & Phased Rollout:**
    *   Structure the project into logical phases with clear deliverables for each.
    *   **Phase 1 (MVP):** Define the minimal feature set for a first functional release (e.g., connect to a single DB, view collections, list existing indexes).
    *   **Phase 2 (Core Functionality):** Detail the features to be added next (e.g., index creation and deletion forms/wizards, multi-connection support).
    *   **Phase 3 (Advanced Features):** Outline future enhancements (e.g., index usage statistics, performance suggestions, user roles).

6.  **Initial Task Breakdown for Phase 1 (MVP):**
    *   Provide a granular list of initial development tasks required to complete the MVP. For each task, include a brief description. Examples: "Setup project scaffolding with Vite and React," "Create Connection form component," "Implement API service for database/collection fetching," "Build read-only Index display table."

7.  **Assumptions, Dependencies, and Risks:**
    *   List key assumptions (e.g., "The backend API is stable, documented, and deployed to a staging environment").
    *   Identify critical dependencies (e.g., "Availability of backend team for API clarifications").
    *   Enumerate potential risks and suggest mitigation strategies (e.g., "Risk: Performance issues when rendering large numbers of indexes. Mitigation: Implement virtualization/pagination for data tables.").
	