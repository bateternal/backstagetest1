# Error Codes Reference

This document provides a comprehensive reference for all error codes used in the Backstage Test Project API.

## HTTP Status Codes

### 2xx Success Codes

| Code | Name | Description |
|------|------|-------------|
| 200 | OK | Request successful |
| 201 | Created | Resource created successfully |
| 202 | Accepted | Request accepted for processing |
| 204 | No Content | Request successful, no content returned |

### 4xx Client Error Codes

| Code | Name | Description | Common Causes |
|------|------|-------------|---------------|
| 400 | Bad Request | Invalid request format | Malformed JSON, missing required fields |
| 401 | Unauthorized | Authentication required | Missing or invalid token |
| 403 | Forbidden | Insufficient permissions | User lacks required permissions |
| 404 | Not Found | Resource not found | Invalid resource ID |
| 405 | Method Not Allowed | HTTP method not supported | Using GET on POST-only endpoint |
| 409 | Conflict | Resource conflict | Duplicate resource creation |
| 422 | Unprocessable Entity | Validation failed | Invalid data format or values |
| 429 | Too Many Requests | Rate limit exceeded | Too many requests in time window |

### 5xx Server Error Codes

| Code | Name | Description | Common Causes |
|------|------|-------------|---------------|
| 500 | Internal Server Error | Unexpected server error | Unhandled exception, database error |
| 502 | Bad Gateway | Upstream server error | External service unavailable |
| 503 | Service Unavailable | Service temporarily unavailable | Maintenance mode, overload |
| 504 | Gateway Timeout | Upstream timeout | External service timeout |

## Application Error Codes

### Authentication Errors

| Code | Description | Resolution |
|------|-------------|------------|
| `AUTH_TOKEN_MISSING` | Authentication token not provided | Include Bearer token in Authorization header |
| `AUTH_TOKEN_INVALID` | Authentication token is invalid | Generate new token or check token format |
| `AUTH_TOKEN_EXPIRED` | Authentication token has expired | Refresh token or generate new one |
| `AUTH_USER_NOT_FOUND` | User associated with token not found | Check user account status |
| `AUTH_PERMISSION_DENIED` | User lacks required permissions | Check user roles and permissions |

### Validation Errors

| Code | Description | Resolution |
|------|-------------|------------|
| `VALIDATION_REQUIRED_FIELD` | Required field is missing | Provide all required fields |
| `VALIDATION_INVALID_FORMAT` | Field format is invalid | Check field format requirements |
| `VALIDATION_VALUE_TOO_LONG` | Field value exceeds maximum length | Reduce field value length |
| `VALIDATION_VALUE_TOO_SHORT` | Field value below minimum length | Increase field value length |
| `VALIDATION_INVALID_CHOICE` | Field value not in allowed choices | Use one of the allowed values |
| `VALIDATION_UNIQUE_CONSTRAINT` | Field value must be unique | Use a different unique value |

### Resource Errors

| Code | Description | Resolution |
|------|-------------|------------|
| `RESOURCE_NOT_FOUND` | Requested resource does not exist | Verify resource ID and existence |
| `RESOURCE_ALREADY_EXISTS` | Resource with same identifier exists | Use different identifier or update existing |
| `RESOURCE_CONFLICT` | Resource state conflict | Resolve conflicting state before operation |
| `RESOURCE_LOCKED` | Resource is locked by another process | Wait for lock to be released |
| `RESOURCE_DEPENDENCY_MISSING` | Required dependency not found | Ensure all dependencies exist |

### Database Errors

| Code | Description | Resolution |
|------|-------------|------------|
| `DATABASE_CONNECTION_ERROR` | Cannot connect to database | Check database service and configuration |
| `DATABASE_QUERY_ERROR` | Database query failed | Check query syntax and parameters |
| `DATABASE_CONSTRAINT_ERROR` | Database constraint violation | Check data integrity requirements |
| `DATABASE_TIMEOUT` | Database operation timed out | Optimize query or increase timeout |
| `DATABASE_LOCK_TIMEOUT` | Database lock acquisition timeout | Retry operation or check for deadlocks |

### External Service Errors

| Code | Description | Resolution |
|------|-------------|------------|
| `EXTERNAL_SERVICE_UNAVAILABLE` | External service is unavailable | Check external service status |
| `EXTERNAL_SERVICE_TIMEOUT` | External service request timed out | Retry request or check service health |
| `EXTERNAL_SERVICE_AUTH_ERROR` | External service authentication failed | Check external service credentials |
| `EXTERNAL_SERVICE_RATE_LIMIT` | External service rate limit exceeded | Implement backoff strategy |

## Error Response Format

### Standard Error Response

```json
{
  "status": "error",
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": {
      "field": "email",
      "issue": "Invalid email format"
    },
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req_123456789"
  }
}
```

### Field Validation Error

```json
{
  "status": "error",
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Multiple validation errors",
    "details": {
      "email": ["This field is required", "Invalid email format"],
      "password": ["Password must be at least 8 characters"],
      "username": ["Username already exists"]
    },
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req_123456789"
  }
}
```

### Authentication Error

```json
{
  "status": "error",
  "error": {
    "code": "AUTH_TOKEN_INVALID",
    "message": "Authentication token is invalid",
    "details": {
      "token_type": "Bearer",
      "expired_at": "2024-01-01T00:00:00Z"
    },
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req_123456789"
  }
}
```

## Error Handling Examples

### Python Client Error Handling

```python
import requests
from requests.exceptions import RequestException

def handle_api_error(response):
    """Handle API error responses"""
    if response.status_code == 400:
        error_data = response.json()['error']
        if error_data['code'] == 'VALIDATION_ERROR':
            print(f"Validation Error: {error_data['message']}")
            if 'details' in error_data:
                for field, issues in error_data['details'].items():
                    print(f"  {field}: {', '.join(issues)}")
        return False
    
    elif response.status_code == 401:
        error_data = response.json()['error']
        if error_data['code'] == 'AUTH_TOKEN_EXPIRED':
            print("Token expired, please refresh")
        elif error_data['code'] == 'AUTH_TOKEN_INVALID':
            print("Invalid token, please re-authenticate")
        return False
    
    elif response.status_code == 404:
        print("Resource not found")
        return False
    
    elif response.status_code == 429:
        print("Rate limit exceeded, please wait")
        return False
    
    elif response.status_code >= 500:
        print("Server error, please try again later")
        return False
    
    return True

# Usage
response = requests.get('http://localhost:8000/api/v1/users/')
if not handle_api_error(response):
    # Handle error case
    pass
else:
    # Process successful response
    users = response.json()['data']
```

### JavaScript Client Error Handling

```javascript
async function handleApiRequest(url, options = {}) {
    try {
        const response = await fetch(url, options);
        
        if (!response.ok) {
            const errorData = await response.json();
            const error = errorData.error;
            
            switch (error.code) {
                case 'VALIDATION_ERROR':
                    console.error('Validation Error:', error.message);
                    if (error.details) {
                        Object.entries(error.details).forEach(([field, issues]) => {
                            console.error(`${field}: ${issues.join(', ')}`);
                        });
                    }
                    break;
                
                case 'AUTH_TOKEN_EXPIRED':
                    console.error('Token expired, redirecting to login');
                    window.location.href = '/login';
                    break;
                
                case 'AUTH_TOKEN_INVALID':
                    console.error('Invalid token, please re-authenticate');
                    break;
                
                case 'RESOURCE_NOT_FOUND':
                    console.error('Resource not found');
                    break;
                
                case 'RATE_LIMIT_EXCEEDED':
                    console.error('Rate limit exceeded, please wait');
                    break;
                
                default:
                    console.error('Unknown error:', error.message);
            }
            
            throw new Error(error.message);
        }
        
        return await response.json();
    } catch (error) {
        console.error('Request failed:', error);
        throw error;
    }
}

// Usage
try {
    const data = await handleApiRequest('/api/v1/users/');
    console.log('Users:', data.data);
} catch (error) {
    // Error already handled in function
}
```

## Rate Limiting

### Rate Limit Headers

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
X-RateLimit-Window: 3600
```

### Rate Limit Error Response

```json
{
  "status": "error",
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded",
    "details": {
      "limit": 1000,
      "remaining": 0,
      "reset_time": "2024-01-01T01:00:00Z",
      "window_seconds": 3600
    },
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req_123456789"
  }
}
```

## Debugging Error Codes

### Enable Debug Mode

```python
# settings.py
DEBUG = True

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['console'],
            'level': 'DEBUG',
        },
    },
}
```

### Error Logging

```python
import logging

logger = logging.getLogger(__name__)

def api_view(request):
    try:
        # API logic here
        pass
    except ValidationError as e:
        logger.error(f"Validation error: {e}")
        return JsonResponse({
            'status': 'error',
            'error': {
                'code': 'VALIDATION_ERROR',
                'message': str(e)
            }
        }, status=400)
    except Exception as e:
        logger.error(f"Unexpected error: {e}")
        return JsonResponse({
            'status': 'error',
            'error': {
                'code': 'INTERNAL_SERVER_ERROR',
                'message': 'An unexpected error occurred'
            }
        }, status=500)
```

## Error Code Testing

### Test Error Responses

```python
# tests.py
from django.test import TestCase
from rest_framework.test import APITestCase
from rest_framework import status

class ErrorCodeTest(APITestCase):
    def test_validation_error(self):
        response = self.client.post('/api/v1/users/', {
            'email': 'invalid-email'  # Invalid format
        })
        
        self.assertEqual(response.status_code, 400)
        error_data = response.json()['error']
        self.assertEqual(error_data['code'], 'VALIDATION_ERROR')
    
    def test_authentication_error(self):
        response = self.client.get('/api/v1/users/')
        # No authentication provided
        
        self.assertEqual(response.status_code, 401)
        error_data = response.json()['error']
        self.assertEqual(error_data['code'], 'AUTH_TOKEN_MISSING')
    
    def test_resource_not_found(self):
        response = self.client.get('/api/v1/users/99999/')
        
        self.assertEqual(response.status_code, 404)
        error_data = response.json()['error']
        self.assertEqual(error_data['code'], 'RESOURCE_NOT_FOUND')
```

## Error Code Monitoring

### Track Error Codes

```python
# middleware.py
import logging
from django.utils.deprecation import MiddlewareMixin

logger = logging.getLogger(__name__)

class ErrorTrackingMiddleware(MiddlewareMixin):
    def process_response(self, request, response):
        if response.status_code >= 400:
            logger.error(f"Error {response.status_code}: {request.path}")
        return response
```

### Error Metrics

```python
# metrics.py
from prometheus_client import Counter

ERROR_COUNTER = Counter(
    'api_errors_total',
    'Total API errors',
    ['error_code', 'endpoint', 'method']
)

def track_error(error_code, endpoint, method):
    ERROR_COUNTER.labels(
        error_code=error_code,
        endpoint=endpoint,
        method=method
    ).inc()
```

## Next Steps

- [Common Issues Guide](common-issues.md)
- [FAQ](faq.md)
- [API Documentation](../api/overview.md)
