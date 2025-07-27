# Vite + Tailwind + Shadcn  

Help me develop a frontend web for this project: **mongo-index-manager**
- Always read and update PROGRESS.md for progress tracking (mark with numbers, characters... for easy reference, mark done/undone to save history, divide into small chunks for easy development and testing)
- System prompt: Always follow system prompt from .bolt/prompt
- I already have a backend service run ats localhost:8216, follow API Documents at: /openapi.yaml so that it can integrate to api service
- Don't need to implement all features at once, just implement pieces carefully, and plan what to do next

**What do you want to build?**
a MongoDB Index Manager helps manage indexes, compare indexes from server and client, sync indexes... 

**Who is it for?**
Internal teams

**What features does it have?** 
(read /openapi.yaml for detail features)
        * : Connection Management (CRUD for MongoDB connection).
        * : Database Explorer (Navigate databases and collections).
        * : Index Management (List, view details, create, and delete indexes).
        * : Compare And Sync Indexes

**Design Guidelines:**
- Modern, minimalist interface
- Vibrant but professional color palette
- Clear visual hierarchy
- Intuitive navigation
- Smooth animations for interactions
- High contrast for accessibility

**Optional Enhancements:**
- Dark/light mode toggle
- Keyboard shortcuts
- Export/import task data

 **Technical Stack Proposal:**
 (let's pick the bests that LLM/model strong at)

 **Technical Stack Proposal:**
 (just recommendations, let's pick the bests that LLM/model strong at)
*   **Framework:** Recommend a modern JavaScript framework (e.g., React with Vite, Next.js, or Vue.js) and provide a brief justification for the choice.
  *   **UI Component Library:** Suggest a suitable library (e.g.,Ant Design, Shadcn/ui, Material-UI) and explain why it fits the project's needs for building data-dense interfaces.    *   **State Management:** Propose a robust state management solution (e.g.,  Zustand,TanStack Query, Pinia, ).
*   **Styling:** Recommend a styling strategy (e.g., CSS-in-JS, Tailwind CSS).
*   **Testing:** Outline a comprehensive testing strategy, specifying tools for unit (Jest/Vitest), component (React Testing Library), and end-to-end (Cypress/Playwright) testing.
---
# PROMPT 1 
You are a Senior Frontend Engineer tasked with designing and initiating the development of a new internal web application, the "Mongo Index Manager."

**Project Overview:**
The application will serve as a centralized interface for internal development teams to manage MongoDB indexes across various environments. It will provide tools for viewing, creating, and synchronizing index configurations, thereby improving database performance and streamlining schema management.

**Core Objective:**
Your primary task is to define the frontend architecture, propose a suitable technology stack, and implement the foundational "Connection Management" feature. All development must follow a structured, incremental approach.

**Functional Requirements:**
The application's features are defined by the API contract. You must strictly adhere to the endpoints and data models specified in the provided `/openapi.yaml` file. The key features include:
1.  **Connection Management:** Full CRUD (Create, Read, Update, Delete) functionality for managing MongoDB connection strings.
2.  **Database & Collection Explorer:** A hierarchical view to navigate through databases and collections for a given connection.
3.  **Index Management:** Functionality to list, view details of, create, and delete indexes for a selected collection.
4.  **Index Comparison & Synchronization:** A tool to compare the indexes defined in a local configuration file against a live database and apply the necessary changes to synchronize them.

**Non-Functional & Design Requirements:**
*   **User Interface:** The UI must be modern, minimalist, and professional. Employ a vibrant yet clean color palette with a strong visual hierarchy to ensure intuitive user flows.
*   **User Experience:** Interactions should be fluid, incorporating subtle animations to provide clear feedback. The application must be highly responsive and performant.
*   **Accessibility:** The design must adhere to WCAG 2.1 Level AA standards, ensuring high contrast ratios and full keyboard navigability.
*   **Extensibility:** Consider future enhancements such as a dark/light mode toggle, global keyboard shortcuts, and data export/import capabilities in your architectural design.

**Technical Stack & Architecture:**
*   **Technology Proposal:** Propose and justify a modern frontend technology stack. Pick technogolies that you are most strong at
*   **API Integration:** All data fetching and mutations must be handled through a dedicated API client layer that interfaces with the endpoints defined in `/openapi.yaml`.

**Development Process & Deliverables:**
This project will be built incrementally. Your work must be tracked in the `PROGRESS.md` file, which will serve as the single source of truth for project status. Use a structured format like a checklist to mark tasks as planned, in-progress, or complete.

Your initial set of deliverables are:
1.  **Technology Stack Recommendation:** A detailed document outlining your proposed stack with clear justifications for each choice.
2.  **Project Structure Plan:** A diagram or tree view of the proposed directory structure for the frontend application.
3.  **Initial Feature Implementation:**
    *   A high-level component breakdown for the "Connection Management" feature.
    *   Production-quality, well-documented code for the UI components required to list, create, edit, and delete connection configurations.
4.  **Updated `PROGRESS.md`:** An updated version of the progress file that:
    *   Marks the planning and initial implementation tasks as complete.
    *   Outlines the plan for the next feature, "Database & Collection Explorer."
---
# PROMPT 2
You are tasked with architecting and developing the frontend for a new web application, **Mongo Index Manager**. This document outlines the project requirements, technical specifications, and development process.

### **1. Project Objective**

Develop a robust, intuitive, and performant frontend application for internal development teams to manage MongoDB indexes. The application will interface with a pre-existing backend service.

### **2. Core Functional Requirements**

The application must be developed in a phased approach. You will implement the following features sequentially, ensuring each phase is fully functional and tested before proceeding to the next.

*   **Phase 1: Application Shell and Connection Management**
    *   Initialize a new React project using Vite and Shadcn/ui.
    *   Set up the basic application layout: a persistent sidebar for navigation and a main content area.
    *   Implement full CRUD (Create, Read, Update, Delete) functionality for managing MongoDB connection configurations. All interactions must align with the specified backend API.
    *   Utilize Zustand for managing the state of connection configurations.

*   **Phase 2: Database and Collection Explorer**
    *   Upon selecting an active connection, the application will fetch and display a hierarchical list of databases and their corresponding collections in the sidebar.
    *   Implement state management to track the currently selected database and collection.

*   **Phase 3: Index Management**
    *   When a user selects a collection, the main content area will display a detailed list of all associated indexes.
    *   Provide UI components to view the detailed definition of an existing index.
    *   Implement forms and logic for creating new indexes and deleting existing ones, based on the API specification.

*   **Phase 4: Index Comparison and Synchronization**
    *   Develop a feature to compare the indexes defined in a local configuration file (or user input) against the indexes on a selected collection on the server.
    *   Display a diff view highlighting missing, extra, or modified indexes.
    *   Provide an interface to generate and execute synchronization commands (e.g., create missing, drop extra) via the backend API.

### **3. Technical Specifications**

*   **Framework:** React 18+ with Vite
*   **UI Component Library:** Shadcn/ui. All UI elements must be built using this library to ensure design consistency.
*   **State Management:** Zustand
*   **Styling:** Tailwind CSS
*   **API Integration:** The frontend must communicate exclusively with the backend service running at `http://localhost:8216`. All API calls must strictly adhere to the contract defined in the project's `/openapi.yaml` file.

### **4. Design and User Experience (UX) Guidelines**

*   **Aesthetics:** The UI must be modern, minimalist, and professional. Employ a vibrant yet clean color palette with a clear visual hierarchy.
*   **Interactivity:** Implement smooth, subtle animations and transitions for user interactions to enhance the user experience.
*   **Accessibility:** The application must comply with WCAG 2.1 Level AA standards, including high-contrast ratios and keyboard navigability.
*   **Layout:** The design must be responsive and optimized for standard desktop resolutions.

### **5. Development Process and Standards**

*   **Project Structure:** Organize the codebase into a scalable, feature-based directory structure 
*   **Code Quality:** Write clean, modular, and well-documented code. Components should be reusable and follow the single-responsibility principle.
*   **Task Management & Progress Tracking:** You must maintain a `PROGRESS.md` file in the project root.
    *   Before beginning work on a new feature or a significant component, add a new entry outlining the plan and sub-tasks.
    *   Update the status of each task (e.g., `[ ] To Do`, `[x] Done`) as you complete it. This creates a transparent and auditable development history.
    *   You will be prompted to confirm completion of each phase before proceeding to the next.

### **6. Post-MVP Enhancements (Backlog)**

The following features are considered out of scope for the initial build but should be considered for future iterations:
*   A dark/light mode theme toggle.
*   Keyboard shortcuts for common actions (e.g., new connection, sync).
*   Functionality to export/import connection configurations and index definitions.
---
# CUSTOM 2:
Help me develop a frontend web for this project: **mongo-index-manager**
- Always read and update PROGRESS.md for progress tracking (mark with numbers, characters... for easy reference, mark done/undone to save history, divide into small chunks for easy development and testing)
- System prompt: Always follow system prompt from .bolt/prompt
- I already have a backend service run ats localhost:8216, follow API Documents at: /openapi.yaml so that it can integrate to api service

**What do you want to build?**
a MongoDB Index Manager helps manage indexes, compare indexes from server and client, sync indexes... 

**What features does it have?** 
* : Connection Management (CRUD for MongoDB connection).
* : Database Explorer (Navigate databases and collections).
* : Index Management (List, view details, create, and delete indexes).
* : Compare And Sync Indexes

**Design Guidelines:**
- Modern, minimalist interface
- Vibrant but professional color palette
- Clear visual hierarchy
- Intuitive navigation
- Smooth animations for interactions
- High contrast for accessibility

 **Technical Stack Proposal:** React with Vite, Tailwind, Shadcn