# Projects Endpoint Examples

This page provides examples of requests and responses for the Projects endpoints: `/`, `/{id}`, and `/{id}` for updates.

---

## Create Project

### Example Request

```json
{
    "name": "Netflix",
    "description": "A site to watch movies"
}
```

> ⚠️ **IMPORTANT**: 
> The `name` field is required and must be non-blank.

### Example Response

The response includes the details of the created project.

```json
{
  "id": 1,
    "name": "Netflix",
    "description": "A site to watch movies",
    "calculationDto": {
        "critical_path": null,
        "estimated_duration": null
    }
}
```

---

## List All Projects

### Example Request

No body is required for this request.

```http
GET /api/projects
```

### Example Response

The response includes a list of all projects in the system.

```json
[
  {
    "id": 1,
    "name": "Netflix",
    "description": "A site to watch movies",
    "calculationDto": {
        "critical_path": null,
        "estimated_duration": null
    }
  },
  {
    "id": 2,
    "name": "Facebook",
    "description": "A site to message your friends",
    "calculationDto": {
        "critical_path": "L-M-N-O-P",
        "estimated_duration": 300
  }
]
```

> 📌 **NOTE**: 
> The `estimated_duration` field is in days.

---

## Get Project by ID

### Example Request

```http
GET /api/projects/1
```

### Example Response

The response includes the details of the requested project.

```json
{
  "id": 1,
    "name": "Netflix",
    "description": "A site to watch movies",
    "calculationDto": {
        "critical_path": null,
        "estimated_duration": null
    }
}
```

---

## Partial Update Project

### Example Request

```http
PATCH /api/projects/1
```

```json
{
    "name": "Netflix",
    "description": "A site to watch movies and live events"
}
```

### Example Response

The response includes the updated details of the project.

```json
{
  "id": 1,
  "name": "Netflix",
  "description": "A site to watch movies and live events",
  "calculationDto": {
        "critical_path": null,
        "estimated_duration": null
    }
}
```

---

## Delete Project

### Example Request

```http
DELETE /api/projects/1
```

### Example Response

No body is returned. The response status will be:

```http
204 No Content
```
