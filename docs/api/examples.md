# API Examples

This section provides practical examples of how to use the Backstage Test Project API.

## Authentication Examples

### Getting an API Token

```python
import requests

# Login to get token
login_data = {
    'username': 'your_username',
    'password': 'your_password'
}

response = requests.post('http://localhost:8000/api/v1/auth/login/', json=login_data)
token = response.json()['token']

# Use token in subsequent requests
headers = {
    'Authorization': f'Bearer {token}',
    'Content-Type': 'application/json'
}
```

### JavaScript/Node.js Example

```javascript
const axios = require('axios');

// Login and get token
const loginResponse = await axios.post('http://localhost:8000/api/v1/auth/login/', {
    username: 'your_username',
    password: 'your_password'
});

const token = loginResponse.data.token;

// Create axios instance with token
const api = axios.create({
    baseURL: 'http://localhost:8000/api/v1',
    headers: {
        'Authorization': `Bearer ${token}`
    }
});
```

## User Management Examples

### Create a User

```python
import requests

user_data = {
    'username': 'john_doe',
    'email': 'john@example.com',
    'first_name': 'John',
    'last_name': 'Doe',
    'password': 'secure_password123'
}

response = requests.post(
    'http://localhost:8000/api/v1/users/',
    json=user_data,
    headers=headers
)

if response.status_code == 201:
    user = response.json()['data']
    print(f"Created user: {user['username']}")
else:
    print(f"Error: {response.json()}")
```

### Get All Users

```python
response = requests.get('http://localhost:8000/api/v1/users/', headers=headers)
users = response.json()['data']

for user in users:
    print(f"User: {user['username']} - {user['email']}")
```

### Update a User

```python
user_id = 1
update_data = {
    'first_name': 'Johnny',
    'last_name': 'Smith'
}

response = requests.put(
    f'http://localhost:8000/api/v1/users/{user_id}/',
    json=update_data,
    headers=headers
)

if response.status_code == 200:
    updated_user = response.json()['data']
    print(f"Updated user: {updated_user['first_name']} {updated_user['last_name']}")
```

### Delete a User

```python
user_id = 1

response = requests.delete(
    f'http://localhost:8000/api/v1/users/{user_id}/',
    headers=headers
)

if response.status_code == 204:
    print("User deleted successfully")
```

## Project Management Examples

### Create a Project

```python
project_data = {
    'name': 'My New Project',
    'description': 'A sample project for demonstration',
    'status': 'active',
    'owner': 1  # User ID
}

response = requests.post(
    'http://localhost:8000/api/v1/projects/',
    json=project_data,
    headers=headers
)

if response.status_code == 201:
    project = response.json()['data']
    print(f"Created project: {project['name']}")
```

### Get Projects with Filtering

```python
# Filter by status
response = requests.get(
    'http://localhost:8000/api/v1/projects/?status=active',
    headers=headers
)

# Filter by owner
response = requests.get(
    'http://localhost:8000/api/v1/projects/?owner=1',
    headers=headers
)

# Multiple filters
response = requests.get(
    'http://localhost:8000/api/v1/projects/?status=active&owner=1',
    headers=headers
)

projects = response.json()['data']
```

### Pagination Example

```python
def get_all_projects(headers, page_size=20):
    all_projects = []
    page = 1
    
    while True:
        response = requests.get(
            f'http://localhost:8000/api/v1/projects/?page={page}&per_page={page_size}',
            headers=headers
        )
        
        data = response.json()
        projects = data['data']
        
        if not projects:
            break
            
        all_projects.extend(projects)
        page += 1
        
        # Check if we've reached the last page
        if page > data['pagination']['pages']:
            break
    
    return all_projects

# Usage
all_projects = get_all_projects(headers)
print(f"Total projects: {len(all_projects)}")
```

## Component Management Examples

### Create a Component

```python
component_data = {
    'name': 'web-frontend',
    'type': 'service',
    'description': 'Frontend web application',
    'project': 1,  # Project ID
    'status': 'active',
    'repository_url': 'https://github.com/example/web-frontend',
    'deployment_url': 'https://web.example.com'
}

response = requests.post(
    'http://localhost:8000/api/v1/components/',
    json=component_data,
    headers=headers
)

if response.status_code == 201:
    component = response.json()['data']
    print(f"Created component: {component['name']}")
```

### Update Component Status

```python
component_id = 1
status_data = {
    'status': 'maintenance'
}

response = requests.patch(
    f'http://localhost:8000/api/v1/components/{component_id}/',
    json=status_data,
    headers=headers
)

if response.status_code == 200:
    component = response.json()['data']
    print(f"Updated component status to: {component['status']}")
```

## Error Handling Examples

### Comprehensive Error Handling

```python
import requests
import time
from requests.exceptions import RequestException, Timeout, ConnectionError

def make_api_request(url, method='GET', data=None, headers=None, max_retries=3):
    """
    Make API request with retry logic and error handling
    """
    for attempt in range(max_retries):
        try:
            if method.upper() == 'GET':
                response = requests.get(url, headers=headers, timeout=30)
            elif method.upper() == 'POST':
                response = requests.post(url, json=data, headers=headers, timeout=30)
            elif method.upper() == 'PUT':
                response = requests.put(url, json=data, headers=headers, timeout=30)
            elif method.upper() == 'DELETE':
                response = requests.delete(url, headers=headers, timeout=30)
            
            # Check for HTTP errors
            if response.status_code >= 400:
                error_data = response.json() if response.content else {}
                print(f"HTTP Error {response.status_code}: {error_data}")
                
                # Don't retry on client errors (4xx)
                if 400 <= response.status_code < 500:
                    return None
                
                # Retry on server errors (5xx)
                if attempt < max_retries - 1:
                    wait_time = (2 ** attempt) + 1
                    print(f"Retrying in {wait_time} seconds...")
                    time.sleep(wait_time)
                    continue
            
            return response
            
        except Timeout:
            print(f"Request timeout (attempt {attempt + 1})")
            if attempt < max_retries - 1:
                time.sleep(2 ** attempt)
                continue
            
        except ConnectionError:
            print(f"Connection error (attempt {attempt + 1})")
            if attempt < max_retries - 1:
                time.sleep(2 ** attempt)
                continue
            
        except RequestException as e:
            print(f"Request error: {e}")
            return None
    
    return None

# Usage example
response = make_api_request(
    'http://localhost:8000/api/v1/users/',
    method='GET',
    headers=headers
)

if response:
    users = response.json()['data']
    print(f"Retrieved {len(users)} users")
else:
    print("Failed to retrieve users after all retries")
```

### Handling Specific Error Types

```python
def handle_api_response(response):
    """
    Handle API response and provide meaningful error messages
    """
    if response.status_code == 200:
        return response.json()['data']
    
    elif response.status_code == 201:
        return response.json()['data']
    
    elif response.status_code == 400:
        error_data = response.json()
        print(f"Validation Error: {error_data['error']['message']}")
        if 'details' in error_data['error']:
            for field, issue in error_data['error']['details'].items():
                print(f"  {field}: {issue}")
        return None
    
    elif response.status_code == 401:
        print("Authentication Error: Invalid or missing token")
        return None
    
    elif response.status_code == 403:
        print("Permission Error: Insufficient permissions")
        return None
    
    elif response.status_code == 404:
        print("Not Found: Resource does not exist")
        return None
    
    elif response.status_code == 429:
        print("Rate Limit Exceeded: Too many requests")
        return None
    
    elif response.status_code >= 500:
        print(f"Server Error: {response.status_code}")
        return None
    
    else:
        print(f"Unexpected Error: {response.status_code}")
        return None
```

## Batch Operations Examples

### Bulk Create Users

```python
def bulk_create_users(users_data, headers):
    """
    Create multiple users in batch
    """
    created_users = []
    failed_users = []
    
    for user_data in users_data:
        response = requests.post(
            'http://localhost:8000/api/v1/users/',
            json=user_data,
            headers=headers
        )
        
        if response.status_code == 201:
            created_users.append(response.json()['data'])
        else:
            failed_users.append({
                'data': user_data,
                'error': response.json()
            })
    
    return created_users, failed_users

# Usage
users_data = [
    {'username': 'user1', 'email': 'user1@example.com', 'password': 'password123'},
    {'username': 'user2', 'email': 'user2@example.com', 'password': 'password123'},
    {'username': 'user3', 'email': 'user3@example.com', 'password': 'password123'},
]

created, failed = bulk_create_users(users_data, headers)
print(f"Created: {len(created)}, Failed: {len(failed)}")
```

### Bulk Update Components

```python
def bulk_update_components(component_updates, headers):
    """
    Update multiple components
    """
    updated_components = []
    
    for component_id, update_data in component_updates.items():
        response = requests.patch(
            f'http://localhost:8000/api/v1/components/{component_id}/',
            json=update_data,
            headers=headers
        )
        
        if response.status_code == 200:
            updated_components.append(response.json()['data'])
        else:
            print(f"Failed to update component {component_id}: {response.json()}")
    
    return updated_components

# Usage
component_updates = {
    1: {'status': 'active'},
    2: {'status': 'maintenance'},
    3: {'status': 'deprecated'}
}

updated = bulk_update_components(component_updates, headers)
print(f"Updated {len(updated)} components")
```

## Webhook Examples

### Setting Up Webhook Endpoints

```python
from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt
from django.views.decorators.http import require_http_methods
import json

@csrf_exempt
@require_http_methods(["POST"])
def webhook_handler(request):
    """
    Handle incoming webhooks
    """
    try:
        payload = json.loads(request.body)
        event_type = payload.get('event')
        data = payload.get('data')
        
        if event_type == 'user.created':
            handle_user_created(data)
        elif event_type == 'user.updated':
            handle_user_updated(data)
        elif event_type == 'project.created':
            handle_project_created(data)
        else:
            print(f"Unknown event type: {event_type}")
        
        return JsonResponse({'status': 'success'})
    
    except json.JSONDecodeError:
        return JsonResponse({'error': 'Invalid JSON'}, status=400)
    except Exception as e:
        print(f"Webhook error: {e}")
        return JsonResponse({'error': 'Internal error'}, status=500)

def handle_user_created(data):
    """Handle user creation webhook"""
    print(f"New user created: {data['username']}")
    # Add your logic here

def handle_user_updated(data):
    """Handle user update webhook"""
    print(f"User updated: {data['username']}")
    # Add your logic here

def handle_project_created(data):
    """Handle project creation webhook"""
    print(f"New project created: {data['name']}")
    # Add your logic here
```

### Testing Webhooks

```python
import requests
import json

def test_webhook():
    """
    Test webhook endpoint
    """
    webhook_url = 'http://localhost:8000/webhooks/'
    
    test_payload = {
        'event': 'user.created',
        'timestamp': '2024-01-01T00:00:00Z',
        'data': {
            'id': 1,
            'username': 'test_user',
            'email': 'test@example.com'
        }
    }
    
    response = requests.post(
        webhook_url,
        json=test_payload,
        headers={'Content-Type': 'application/json'}
    )
    
    if response.status_code == 200:
        print("Webhook test successful")
    else:
        print(f"Webhook test failed: {response.status_code}")

# Run test
test_webhook()
```

## Performance Optimization Examples

### Caching API Responses

```python
import requests
from functools import lru_cache
import time

@lru_cache(maxsize=128)
def get_cached_user(user_id, token_hash):
    """
    Get user with caching
    """
    headers = {'Authorization': f'Bearer {token_hash}'}
    response = requests.get(f'http://localhost:8000/api/v1/users/{user_id}/', headers=headers)
    
    if response.status_code == 200:
        return response.json()['data']
    return None

# Usage
token = 'your-token-here'
user = get_cached_user(1, token)
print(f"Cached user: {user['username']}")
```

### Concurrent API Requests

```python
import asyncio
import aiohttp
import time

async def fetch_user(session, user_id, token):
    """
    Fetch a single user asynchronously
    """
    headers = {'Authorization': f'Bearer {token}'}
    async with session.get(f'http://localhost:8000/api/v1/users/{user_id}/', headers=headers) as response:
        if response.status == 200:
            data = await response.json()
            return data['data']
        return None

async def fetch_multiple_users(user_ids, token):
    """
    Fetch multiple users concurrently
    """
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_user(session, user_id, token) for user_id in user_ids]
        users = await asyncio.gather(*tasks)
        return [user for user in users if user is not None]

# Usage
async def main():
    user_ids = [1, 2, 3, 4, 5]
    token = 'your-token-here'
    
    start_time = time.time()
    users = await fetch_multiple_users(user_ids, token)
    end_time = time.time()
    
    print(f"Fetched {len(users)} users in {end_time - start_time:.2f} seconds")

# Run async function
asyncio.run(main())
```

## Next Steps

- [API Overview](overview.md)
- [Authentication Details](authentication.md)
- [Complete Endpoint Reference](endpoints.md)
