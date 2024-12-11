# Request and Response Examples: CPM and Network Diagram Endpoint

This page provides examples of requests and responses for the CPM endpoints: `/project/{id}`, `/project/diagram/{id}`, and `/project/{id}` for retrieving, updating, and generating critical path calculations and network diagrams.

## Generate Critical Path and Network Diagram

### Example Request (POST `/project/{id}`)

Endpoint to calculate and generate the network diagram for the specified project. Replace `{id}` with the project ID.

#### Request URL

```http
POST http://localhost:8080/api/calculations/project/1
```

#### Example Response 

A Base64-encoded string representing the network diagram image is returned.

```json
{
  "base64_image": "iVBORw0KGgoAAAANSUhEUgAAAGQAAAAuCAYAAAAZcJbaAAAAAXNSR0IArs4c6QAA"
}
```

---

## Update and Retrieve Network Diagram

### Example Request (PATCH `/project/diagram/{id}`)

Endpoint to update existing calculations and regenerate the network diagram for the specified project. Replace `{id}` with the project ID.

#### Request URL

```http
PATCH http://localhost:8080/api/calculations/project/diagram/1
```

#### Example Response

A Base64-encoded string representing the updated network diagram image is returned.

```json
{
  "base64_image": "iVBORw0KGgoAAAANSUhEUgAAAGQAAAAuCAYAAAAZcJbaAAAAAXNSR0IArs4c6QAA"
}
```

---

## Retrieve Calculations

### Example Request (GET `/project/{id}`)

Endpoint to fetch the calculation details for the specified project. Replace `{id}` with the project ID.

#### Request URL

```http
GET http://localhost:8080/api/calculations/project/1
```

#### Example Response

A detailed JSON object containing the calculation information, including the critical path and estimated duration.

```json
{
  "id": 1,
  "criticalPath": "B-D-E-G",
  "estimatedDuration": 26,
  "projectDto": 
    {
      "id": 1,
      "name": "FitnessTracker",
      "description": "A project to keep track of your gym PRs",
      "userDto": {
            "id": 1,
            "fullName": "Pepe Le Pu",
            "username": "pepepu",
            "email": "pepe@gmail.com",
            "dateOfBirth": "1996-04-19" // note the yyyy-MM-dd format
      }
    }
  
}
```

---

## Retrieve Network Diagram

### Example Request (GET `/project/diagram/{id}`)

Endpoint to regenerate and retrieve network diagram for the specified project. Replace `{id}` with the project ID.

#### Request URL

```http
GET http://localhost:8080/api/calculations/project/diagram/1
```

#### Example Response

A Base64-encoded string representing the network diagram image is returned.

```json
{
  "base64_image": "iVBORw0KGgoAAAANSUhEUgAAAGQAAAAuCAYAAAAZcJbaAAAAAXNSR0IArs4c6QAA"
}
```

---
