# Overview: Projects Endpoint

The Projects Endpoint provides functionalities to create, retrieve, update, and delete projects within the system. It integrates with the service layer to apply business logic and data transformations as needed.

## Base URL

All project-related endpoints are available under the following base URL:

```bash
http://localhost:8080/api/projects
```

## Available Endpoints

### **Create Project** (`POST /`)

Creates a new project with the provided details.

**Request Body:**

- **name**: Name of the project (required, non-blank).
- **description**: Detailed description of the project (optional).

**Responses:**

- `201 CREATED`: Returns the created project.
- `400 BAD REQUEST`: If any validation fails.

**Behavior:**

- Maps the incoming request to a `Project` entity.
- Saves the project in the database.

---

### **List All Projects** (`GET /`)

Retrieves a list of all projects in the system.

**Responses:**

- `200 OK`: Returns a list of all projects.

**Behavior:**

- Maps each project entity to a `ProjectDto` for the response.

---

### **Get Project by ID** (`GET /{id}`)

Retrieves a specific project by its ID.

**Path Parameters:**

- **id**: ID of the project (required).

**Responses:**

- `200 OK`: Returns the requested project.
- `404 NOT FOUND`: If the project does not exist.

**Behavior:**

- Searches for the project in the database by its ID and maps it to a `ProjectDto` if found.

---

### **Full Update Project** (`PUT /{id}`)

Performs a complete update of an existing project.

**Path Parameters:**

- **id**: ID of the project to update (required).

**Request Body:**

- **name**: Name of the project (required, non-blank).
- **description**: Detailed description of the project (optional).

**Responses:**

- `200 OK`: Returns the updated project.
- `404 NOT FOUND`: If the project does not exist.

**Behavior:**

- Validates the project's existence.
- Maps the request body to a `Project` entity and updates it in the database.

---

### **Partial Update Project** (`PATCH /{id}`)

Performs a partial update of an existing project.

**Path Parameters:**

- **id**: ID of the project to update (required).

**Request Body:**

- **name**: Updated name of the project (optional, non-blank).
- **description**: Updated description of the project (optional).

**Responses:**

- `200 OK`: Returns the updated project.
- `404 NOT FOUND`: If the project does not exist.

**Behavior:**

- Validates the project's existence.
- Maps the request body to a `Project` entity and applies partial updates.

---

### **Delete Project** (`DELETE /{id}`)

Deletes a project by its ID.

**Path Parameters:**

- **id**: ID of the project to delete (required).

**Responses:**

- `204 NO CONTENT`: If the project was successfully deleted or it doesn't exists.

**Behavior:**

- Deletes the project from the database.