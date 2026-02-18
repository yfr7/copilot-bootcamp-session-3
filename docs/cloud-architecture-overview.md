# Cloud Architecture Overview

## System Context

The TODO application is a monorepo consisting of a React frontend, an Express API backend, and an in-memory SQLite data store. The user interacts with the React UI in the browser, which communicates with the Express API over HTTP. The API reads and writes task data to the in-memory store.

```mermaid
graph TD
    User["👤 User"]
    Frontend["React Frontend<br/>(packages/frontend)"]
    Backend["Express API<br/>(packages/backend)"]
    Store["In-Memory SQLite Store"]

    User -->|Interacts with| Frontend
    Frontend -->|HTTP Requests<br/>/api/tasks| Backend
    Backend -->|Read/Write| Store
```

## Sequence Diagram: Creating a TODO

The following diagram shows the end-to-end flow when a user creates a new task.

```mermaid
sequenceDiagram
    actor User
    participant Frontend as React Frontend
    participant API as Express API
    participant DB as In-Memory SQLite

    User->>Frontend: Fills in task title, description, due date, priority
    User->>Frontend: Clicks "Add Task"
    Frontend->>Frontend: Validates title is not empty
    Frontend->>API: POST /api/tasks<br/>{ title, description, due_date, priority }
    API->>API: Validates title is required
    API->>DB: INSERT INTO tasks (title, description, due_date, priority)
    DB-->>API: Returns new row with id
    API->>API: SELECT * FROM tasks WHERE id = lastInsertRowid
    API-->>Frontend: 201 Created { id, title, description, due_date, priority, completed, created_at }
    Frontend->>API: GET /api/tasks
    API->>DB: SELECT * FROM tasks
    DB-->>API: Returns all tasks
    API-->>Frontend: 200 OK [tasks]
    Frontend-->>User: Displays updated task list
```
