# Overview: Activities Endpoint

The Activities Endpoint provides functionalities for creating, retrieving, updating, and deleting activities within the system. It ensures robust validation for inputs and maintains data integrity by applying specific business rules.

## Base URL

All activities-related endpoints are available under the following base URL:

```bash
http://localhost:8080/api/activities
```

## Available Endpoints

### **Create Activity** (`POST /`)

Creates a new activity with the provided details.

**Request Body:**

- **name**: Name of the activity (required, non-blank).
- **predecessors**: Comma-separated list of predecessor labels (optional, must match the regex pattern if provided).
- **daysDuration**: Duration of the activity in days (required, must be greater than 0).
- **projectDto.id**: ID of the associated project (required, must be greater than 0).

**Responses:**

- `201 CREATED`: Returns the created activity.
- `400 BAD REQUEST`: If any validation fails.

**Behavior:**

- Validates that all required fields are provided and adhere to constraints.
- Saves the activity to the database.

---

### **List All Activities** (`GET /`)

Retrieves a list of all activities in the system.

**Responses:**

- `200 OK`: Returns a list of all activities.

**Behavior:**

- Maps each activity entity to a `ActivityDto` for the response.

---

### **Get Activity by ID** (`GET /{id}`)

Retrieves a specific activity by its ID.

**Path Parameters:**

- **id**: ID of the activity (required).

**Responses:**

- `200 OK`: Returns the requested activity.
- `404 NOT FOUND`: If the activity does not exist.

---

### **Get Activities by Project ID** (`GET /project/{id}`)

Retrieves all activities associated with a specific project.

**Path Parameters:**

- **id**: ID of the project (required, must be greater than 0).

**Responses:**

- `200 OK`: Returns a list of activities for the specified project.
- `400 BAD REQUEST`: If the project ID is invalid.

**Behavior:**

- Fetches activities related to the specified project and maps them to `ActivityDto`.

---

### **Partial Update Activity** (`PATCH /{id}`)

Partially updates the details of an existing activity.

**Path Parameters:**

- **id**: ID of the activity to update (required).

**Request Body:**

- **name**: Name of the activity (optional, must not be blank if provided).
- **predecessors**: Comma-separated list of predecessor labels (optional, must match the regex pattern if provided).
- **daysDuration**: Duration of the activity in days (optional, must be greater than 0).

**Responses:**

- `200 OK`: Returns the updated activity.
- `400 BAD REQUEST`: If any validation fails.
- `404 NOT FOUND`: If the activity does not exist.

**Behavior:**

- Validates input fields, applies partial updates, and ensures all business rules are followed.

---

### **Delete Activity** (`DELETE /{id}`)

Deletes an activity by its ID and all its appearances in any predecessors from the same project.

**Path Parameters:**

- **id**: ID of the activity to delete (required).

**Responses:**

- `204 NO CONTENT`: If the activity was successfully deleted.
- `404 NOT FOUND`: If the activity does not exist.

**Behavior:**

- Deletes the activity from the database and all its appearances in any predecessors from the same project.

---