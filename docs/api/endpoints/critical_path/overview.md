# Overview: Critical Path Method (CPM) and Network Diagram Endpoint

The Critical Path Method (CPM) and Network Diagram Endpoint is designed to manage calculations related to project schedules and generate visual representations of project networks. It allows to compute the critical path, validate activity sets, and retrieve or update network diagrams in a secure and efficient manner.

## Functionality Overview

This endpoint suite enables to:

1. Calculate the critical path of a project.
2. Generate a network diagram of the project's tasks, highlighting the critical path.
3. Retrieve stored calculations for a project.
4. Update diagrams based on modifications to project activities.

The service ensures all project activities are complete before performing calculations or generating diagrams, maintaining data integrity and accuracy.

## Base URL

All endpoints related to CPM and network diagrams are accessible under the following base URL:

```bash
http://localhost:8080/api/calculations
```

## Available Endpoints

**Create and Retrieve Network Diagram** (`POST /project/{id}`)

Generates the critical path and network diagram for a project.

- **Path Parameters**:
    - `id`: The ID of the project (must be a positive integer).
- **Behavior**:
    - Validates that all project activities exist and there is not a missing one.
    - Performs all calculations required to determine the critical path.
    - Generates and returns a network diagram encoded as a Base64 string.
- **Response**:
    - `200 OK`: Base64-encoded image of the network diagram.
    - `400 Bad Request`: If the project ID is invalid.
    - `500 Internal Server Error`: If an error occurs during calculation or diagram generation.

---

**Update and Retrieve Network Diagram** (`PATCH /project/diagram/{id}`)

Updates the critical path calculations and regenerates the network diagram for a project.

- **Path Parameters**:
    - `id`: The ID of the project (must be a positive integer).
- **Behavior**:
    - Validates that all project activities exist and there is not a missing one.
    - Updates the existing calculations and regenerates the diagram.
    - Returns the updated network diagram as a Base64 string.
- **Response**:
    - `200 OK`: Base64-encoded updated image of the network diagram.
    - `400 Bad Request`: If the project ID is invalid.
    - `500 Internal Server Error`: If an error occurs during calculation or diagram regeneration.

---

**Retrieve Calculations** (`GET /project/{id}`)

Fetches the stored calculations for a project by its ID.

- **Path Parameters**:
    - `id`: The ID of the project (must be a positive integer).
- **Behavior**:
    - Retrieves the calculation data for the specified project.
    - Returns the data as a `CalculationDto` object.
- **Response**:
    - `200 OK`: JSON representation of the calculation data.
    - `400 Bad Request`: If the project ID is invalid.
    - `404 Not Found`: If no calculations exist for the specified project.

---

**Retrieve Network Diagram** (`GET /project/diagram/{id}`)

Fetches the network diagram for a project.

- **Path Parameters**:
    - `id`: The ID of the project (must be a positive integer).
- **Behavior**:
    - Validates that all project activities exist and there is not a missing one.
    - Regenerates and retrieves the network diagram encoded as a Base64 string.
- **Response**:
    - `200 OK`: Base64-encoded image of the network diagram.
    - `400 Bad Request`: If the project ID is invalid.
    - `500 Internal Server Error`: If an error occurs during diagram retrieval.

---

**Exception Handling**

All endpoints handle `EntityNotFoundException` gracefully:

- **Response**:
    - `404 Not Found`: If the specified project or calculation does not exist, with a descriptive error message.

---
