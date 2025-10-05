# Development Setup

This guide covers setting up a development environment for the Backstage Test Project.

## Prerequisites

Before setting up the development environment, ensure you have:

- **Python 3.8+** installed
- **Git** for version control
- **Virtual environment** support
- **Code editor** (VS Code, PyCharm, etc.)
- **Terminal/Command prompt** access

## Environment Setup

### 1. Clone the Repository

```bash
# Clone your fork
git clone https://github.com/your-username/backstagetest1.git
cd backstagetest1

# Add upstream remote
git remote add upstream https://github.com/bateternal/backstagetest1.git
```

### 2. Create Virtual Environment

```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# On macOS/Linux:
source venv/bin/activate

# On Windows:
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
# Install production dependencies
pip install -r requirements.txt

# Install development dependencies
pip install -r requirements-dev.txt

# Install pre-commit hooks
pre-commit install
```

### 4. Environment Configuration

Create a `.env` file for local development:

```bash
# Copy example environment file
cp .env.example .env
```

Edit `.env` with your local settings:

```env
# Django Settings
DEBUG=True
SECRET_KEY=your-development-secret-key-here
ALLOWED_HOSTS=localhost,127.0.0.1

# Database (SQLite for development)
DATABASE_URL=sqlite:///db.sqlite3

# Cache (Local memory for development)
CACHE_URL=locmem://

# Backstage Integration
BACKSTAGE_URL=http://localhost:7007

# Email (Console backend for development)
EMAIL_BACKEND=django.core.mail.backends.console.EmailBackend

# Logging
LOG_LEVEL=DEBUG
```

### 5. Database Setup

```bash
# Run migrations
python manage.py migrate

# Create superuser (optional)
python manage.py createsuperuser

# Load sample data (if available)
python manage.py loaddata fixtures/sample_data.json
```

### 6. Static Files

```bash
# Collect static files
python manage.py collectstatic --noinput
```

## Development Tools

### Code Editor Configuration

#### VS Code Setup

Install these extensions:
- Python
- Django
- GitLens
- REST Client
- Markdown All in One
- Mermaid Preview

Create `.vscode/settings.json`:
```json
{
    "python.defaultInterpreterPath": "./venv/bin/python",
    "python.linting.enabled": true,
    "python.linting.flake8Enabled": true,
    "python.formatting.provider": "black",
    "python.sortImports.args": ["--profile", "black"],
    "files.exclude": {
        "**/__pycache__": true,
        "**/*.pyc": true,
        "**/node_modules": true,
        "**/.git": true
    }
}
```

#### PyCharm Setup

1. **Configure Python Interpreter:**
   - Go to Settings → Project → Python Interpreter
   - Select the virtual environment interpreter

2. **Enable Django Support:**
   - Go to Settings → Languages & Frameworks → Django
   - Enable Django support
   - Set Django project root to project directory

3. **Configure Code Style:**
   - Go to Settings → Editor → Code Style → Python
   - Import Black code style scheme

### Git Configuration

Set up Git for the project:

```bash
# Configure user (if not already set globally)
git config user.name "Your Name"
git config user.email "your.email@example.com"

# Set up branch protection
git config branch.main.remote upstream
git config branch.main.merge refs/heads/main
```

### Pre-commit Hooks

Pre-commit hooks are automatically installed and will run on every commit:

```bash
# Manual run of all hooks
pre-commit run --all-files

# Skip hooks for a commit
git commit --no-verify -m "Emergency fix"
```

## Development Workflow

### Daily Development Routine

1. **Start development server:**
   ```bash
   python manage.py runserver
   ```

2. **Run tests before making changes:**
   ```bash
   python manage.py test
   ```

3. **Make your changes** following coding standards

4. **Run tests after changes:**
   ```bash
   python manage.py test
   coverage run --source='.' manage.py test
   coverage report
   ```

5. **Check code quality:**
   ```bash
   flake8 .
   black --check .
   isort --check-only .
   mypy .
   ```

6. **Commit changes:**
   ```bash
   git add .
   git commit -m "feat: add your feature"
   ```

### Branch Management

```bash
# Create feature branch
git checkout -b feature/your-feature-name

# Create bugfix branch
git checkout -b bugfix/issue-description

# Create documentation branch
git checkout -b docs/update-guide

# Sync with upstream
git fetch upstream
git rebase upstream/main
```

### Testing Workflow

#### Running Tests

```bash
# Run all tests
python manage.py test

# Run specific app tests
python manage.py test myapp

# Run specific test
python manage.py test myapp.tests.TestClass.test_method

# Run with coverage
coverage run --source='.' manage.py test
coverage report
coverage html  # Generate HTML report
```

#### Writing Tests

```python
# tests.py
from django.test import TestCase
from django.urls import reverse
from rest_framework.test import APITestCase
from rest_framework import status

class MyModelTest(TestCase):
    def setUp(self):
        self.model = MyModel.objects.create(name='Test')
    
    def test_model_creation(self):
        self.assertEqual(self.model.name, 'Test')
    
    def test_model_str(self):
        self.assertEqual(str(self.model), 'Test')

class MyAPITest(APITestCase):
    def setUp(self):
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass123'
        )
        self.client.force_authenticate(user=self.user)
    
    def test_api_endpoint(self):
        response = self.client.get('/api/v1/endpoint/')
        self.assertEqual(response.status_code, status.HTTP_200_OK)
```

## Debugging Tools

### Django Debug Toolbar

The Django Debug Toolbar is included for development:

```python
# settings/dev.py
if DEBUG:
    INSTALLED_APPS += ['debug_toolbar']
    MIDDLEWARE += ['debug_toolbar.middleware.DebugToolbarMiddleware']
    INTERNAL_IPS = ['127.0.0.1']
```

### Logging Configuration

Development logging configuration:

```python
# settings/dev.py
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
            'level': 'INFO',
        },
        'myapp': {
            'handlers': ['console'],
            'level': 'DEBUG',
        },
    },
}
```

### Database Debugging

```bash
# Django shell
python manage.py shell

# Database shell
python manage.py dbshell

# Show SQL queries
python manage.py shell
>>> from django.db import connection
>>> connection.queries
```

## API Development

### API Testing

Use the REST Client extension in VS Code or tools like Postman:

```http
### Get all users
GET http://localhost:8000/api/v1/users/
Authorization: Bearer your-token-here

### Create user
POST http://localhost:8000/api/v1/users/
Content-Type: application/json
Authorization: Bearer your-token-here

{
    "username": "newuser",
    "email": "newuser@example.com",
    "password": "password123"
}
```

### API Documentation

Document API endpoints:

```python
# views.py
from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response

@api_view(['GET'])
def user_list(request):
    """
    List all users.
    
    Returns a list of all users in the system.
    
    Query Parameters:
    - page: Page number (default: 1)
    - per_page: Items per page (default: 20)
    
    Returns:
    - 200: List of users
    - 401: Authentication required
    """
    # Implementation here
    pass
```

## Documentation Development

### Local Documentation Server

```bash
# Install MkDocs
pip install mkdocs mkdocs-material

# Serve documentation locally
mkdocs serve

# Build documentation
mkdocs build
```

### Documentation Standards

- Use clear, concise language
- Include code examples
- Add diagrams for complex concepts
- Keep documentation up-to-date
- Follow the existing structure

### Adding New Documentation

1. Create new `.md` file in appropriate directory
2. Add to `docs/mkdocs.yml` navigation
3. Use proper markdown formatting
4. Include diagrams and examples
5. Test locally with `mkdocs serve`

## Performance Optimization

### Database Optimization

```python
# Use select_related for foreign keys
users = User.objects.select_related('profile')

# Use prefetch_related for many-to-many
projects = Project.objects.prefetch_related('tags')

# Use only() to limit fields
users = User.objects.only('username', 'email')
```

### Caching

```python
# Use cache in views
from django.core.cache import cache

def expensive_view(request):
    cache_key = f'expensive_data_{request.user.id}'
    data = cache.get(cache_key)
    
    if data is None:
        data = expensive_calculation()
        cache.set(cache_key, data, 300)  # 5 minutes
    
    return JsonResponse(data)
```

## Troubleshooting Development Issues

### Common Problems

1. **Virtual environment not activated:**
   ```bash
   which python  # Should show venv path
   ```

2. **Dependencies not found:**
   ```bash
   pip list  # Check installed packages
   ```

3. **Database connection issues:**
   ```bash
   python manage.py check --database default
   ```

4. **Static files not loading:**
   ```bash
   python manage.py collectstatic --noinput
   ```

### Getting Help

- Check the [Troubleshooting Guide](../troubleshooting/common-issues.md)
- Search [GitHub Issues](https://github.com/bateternal/backstagetest1/issues)
- Ask in the #development Slack channel
- Create a new issue for specific problems

## Next Steps

- [Coding Standards](coding-standards.md)
- [Testing Guide](testing.md)
- [Debugging Techniques](debugging.md)
