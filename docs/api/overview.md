# API Overview

The Backstage Test Project provides a RESTful API for managing various resources. This section provides an overview of the API capabilities, authentication, and general usage patterns.

## Base URL

```
http://localhost:8000/api/v1/
```

## Authentication

The API uses token-based authentication. Include your token in the Authorization header:

```http
Authorization: Bearer your-token-here
```

### Getting an API Token

1. Log in to the Django admin interface
2. Navigate to Users → API Tokens
3. Create a new token
4. Copy the token for use in API requests

## Response Format

All API responses follow a consistent JSON format:

### Success Response
```json
{
  "status": "success",
  "data": {
    "id": 1,
    "name": "Example Resource",
    "created_at": "2024-01-01T00:00:00Z"
  },
  "message": "Resource retrieved successfully"
}
```

### Error Response
```json
{
  "status": "error",
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": {
      "field": "name",
      "issue": "This field is required"
    }
  }
}
```

## HTTP Status Codes

| Code | Description |
|------|-------------|
| 200 | OK - Request successful |
| 201 | Created - Resource created successfully |
| 400 | Bad Request - Invalid input |
| 401 | Unauthorized - Authentication required |
| 403 | Forbidden - Insufficient permissions |
| 404 | Not Found - Resource not found |
| 500 | Internal Server Error - Server error |

## Pagination

List endpoints support pagination:

```json
{
  "status": "success",
  "data": [...],
  "pagination": {
    "page": 1,
    "per_page": 20,
    "total": 100,
    "pages": 5
  }
}
```

### Pagination Parameters

- `page`: Page number (default: 1)
- `per_page`: Items per page (default: 20, max: 100)

## Filtering and Sorting

Most list endpoints support filtering and sorting:

### Filtering
```
GET /api/v1/resources?status=active&category=web
```

### Sorting
```
GET /api/v1/resources?sort=name&order=desc
```

## Rate Limiting

API requests are rate limited to prevent abuse:

- **Authenticated users**: 1000 requests per hour
- **Unauthenticated users**: 100 requests per hour

Rate limit headers are included in responses:

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
```

## API Endpoints

### Core Resources

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/users` | GET, POST | User management |
| `/users/{id}` | GET, PUT, DELETE | Individual user operations |
| `/projects` | GET, POST | Project management |
| `/projects/{id}` | GET, PUT, DELETE | Individual project operations |
| `/components` | GET, POST | Component management |
| `/components/{id}` | GET, PUT, DELETE | Individual component operations |

### Utility Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Health check |
| `/status` | GET | System status |
| `/metrics` | GET | System metrics |

## SDKs and Libraries

### Python
```python
import requests

headers = {
    'Authorization': 'Bearer your-token-here',
    'Content-Type': 'application/json'
}

response = requests.get(
    'http://localhost:8000/api/v1/users',
    headers=headers
)
```

### JavaScript/Node.js
```javascript
const axios = require('axios');

const api = axios.create({
  baseURL: 'http://localhost:8000/api/v1',
  headers: {
    'Authorization': 'Bearer your-token-here'
  }
});

const users = await api.get('/users');
```

### cURL Examples
```bash
# Get all users
curl -H "Authorization: Bearer your-token-here" \
     http://localhost:8000/api/v1/users

# Create a new user
curl -X POST \
     -H "Authorization: Bearer your-token-here" \
     -H "Content-Type: application/json" \
     -d '{"name": "John Doe", "email": "john@example.com"}' \
     http://localhost:8000/api/v1/users
```

## Webhooks

The API supports webhooks for real-time notifications:

### Supported Events
- `user.created`
- `user.updated`
- `user.deleted`
- `project.created`
- `project.updated`
- `project.deleted`

### Webhook Payload
```json
{
  "event": "user.created",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```

## Error Handling

### Common Error Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `VALIDATION_ERROR` | Invalid input data | Check request payload |
| `AUTHENTICATION_REQUIRED` | Missing or invalid token | Provide valid token |
| `PERMISSION_DENIED` | Insufficient permissions | Check user permissions |
| `RESOURCE_NOT_FOUND` | Requested resource doesn't exist | Verify resource ID |
| `RATE_LIMIT_EXCEEDED` | Too many requests | Wait and retry |

### Retry Logic

For transient errors (5xx), implement exponential backoff:

```python
import time
import random

def retry_request(func, max_retries=3):
    for attempt in range(max_retries):
        try:
            return func()
        except requests.exceptions.RequestException as e:
            if attempt == max_retries - 1:
                raise
            wait_time = (2 ** attempt) + random.uniform(0, 1)
            time.sleep(wait_time)
```

## Testing the API

### Using Postman

1. Import the API collection from `/docs/postman/collection.json`
2. Set up environment variables:
   - `base_url`: `http://localhost:8000/api/v1`
   - `token`: Your API token
3. Run the collection tests

### Using Insomnia

1. Import the API specification from `/docs/insomnia/workspace.json`
2. Configure authentication
3. Test endpoints interactively

## Next Steps

- [Authentication Details](authentication.md)
- [Complete Endpoint Reference](endpoints.md)
- [Practical Examples](examples.md)
