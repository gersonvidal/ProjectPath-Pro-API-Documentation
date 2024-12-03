# Activities Endpoint Examples

This page provides examples of requests and responses for the Activities endpoints: `/`, `/{id}`, `/project/{id}`, and `/{id}` for partial updates.

---

## Create Activity

### Example Request

```json
{
  "name": "Project Initialization",
  "predecessors": "B,C",
  "daysDuration": 5,
  "projectDto": {
    "id": 1
  }
}
```

> ⚠️ **IMPORTANT**: 
> The `projectDto.id` must be a positive integer.

### Example Response

The response includes the details of the created activity.

```json
{
  "id": 1,
  "name": "Project Initialization",
  "label": "A",
  "predecessors": "B,C",
  "daysDuration": 5,
  "closeStart": null,
  "distantStart": null,
  "closeFinish": null,
  "distantFinish": null,
  "slack": null,
  "projectDto": {
    "id": 1,
    "name": null,
    "description": null
  }
}
```

> 📌 **NOTE**: 
> The `projectDto.name` and `projectDto.description` will return null, but if the `projectDto.id` is valid then the activity will be automatically assigned to that project.

---

## List All Activities

### Example Request

No body is required for this request.

### Example Response

The response includes a list of all activities in the system.

```json
[
  {
    "id": 1,
    "name": "Project Initialization",
    "label": "A",
    "predecessors": "B,C",
    "daysDuration": 5,
    "closeStart": null, // or some number
    "distantStart": null, // or some number
    "closeFinish": null, // or some number
    "distantFinish": null, // or some number
    "slack": null, // or some number
    "projectDto": {
      "id": 1, 
      "name": "Netflix",
      "description": "A site to watch movies"
    }
  },
  {
    "id": 2,
    "name": "Execution Phase",
    "label": "B",
    "predecessors": "A",
    "daysDuration": 10,
    "closeStart": null, // or some number
    "distantStart": null, // or some number
    "closeFinish": null, // or some number
    "distantFinish": null, // or some number
    "slack": null, // or some number
    "projectDto": {
      "id": 1,
      "name": "Netflix",
      "description": "A site to watch movies"
    }
  }
]
```

---

## Get Activity by ID

### Example Request

```http
GET /api/activities/1
```

### Example Response

The response includes the details of the requested activity.

```json
{
  "id": 1,
  "name": "Project Initialization",
  "label": "A",
  "predecessors": "B,C",
  "daysDuration": 5,
  "closeStart": null, // or some number
  "distantStart": null, // or some number
  "closeFinish": null, // or some number
  "distantFinish": null, // or some number
  "slack": null, // or some number
  "projectDto": {
      "id": 1,
      "name": "Netflix",
      "description": "A site to watch movies"
    }
}
```

---

## Get Activities by Project ID

### Example Request

```http
GET /api/activities/project/1
```

### Example Response

The response includes all activities associated with the specified project.

```json
[
  {
    "id": 1,
    "name": "Project Initialization",
    "label": "A",
    "predecessors": "B,C",
    "daysDuration": 5,
    "closeStart": null, // or some number
    "distantStart": null, // or some number
    "closeFinish": null, // or some number
    "distantFinish": null, // or some number
    "slack": null, // or some number
    "projectDto": {
        "id": 1,
        "name": "Netflix",
        "description": "A site to watch movies"
        }
  },
  {
    "id": 2,
    "name": "Execution Phase",
    "label": "B",
    "predecessors": "A",
    "daysDuration": 10,
    "closeStart": null, // or some number
    "distantStart": null, // or some number
    "closeFinish": null, // or some number
    "distantFinish": null, // or some number
    "slack": null, // or some number
    "projectDto": {
        "id": 1,
        "name": "Netflix",
        "description": "A site to watch movies"
        }
  }
]
```

---

## Partial Update Activity

### Example Request

```http
PATCH /api/activities/1
```

```json
{
  "name": "Updated Initialization",
  "predecessors": "C,D", // or null
  "daysDuration": 7
}
```

### Example Response

The response includes the updated details of the activity.

```json
{
  "id": 1,
  "name": "Updated Initialization",
  "label": "A",
  "predecessors": "C,D",
  "daysDuration": 7,
  "closeStart": null, // or some number
  "distantStart": null, // or some number
  "closeFinish": null, // or some number
  "distantFinish": null, // or some number
  "slack": null, // or some number
  "projectDto": {
        "id": 1,
        "name": "Netflix",
        "description": "A site to watch movies"
        }
}
```

---

## Delete Activity

### Example Request

```http
DELETE /api/activities/1
```

### Example Response

No body is returned. The response status will be:

```http
204 No Content
```
