# Installation Guide

This guide will walk you through installing and setting up the Backstage Test Project on your local machine.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Python 3.8+**: Download from [python.org](https://python.org)
- **Git**: Download from [git-scm.com](https://git-scm.com)
- **Virtual Environment**: Usually included with Python
- **Backstage Instance**: Local or hosted Backstage instance

### System Requirements

| Component | Minimum Version | Recommended |
|-----------|----------------|-------------|
| Python | 3.8 | 3.11+ |
| Django | 5.0 | 5.2+ |
| Node.js | 16.0 | 18.0+ |
| Git | 2.0 | Latest |

## Installation Steps

### 1. Clone the Repository

```bash
git clone https://github.com/bateternal/backstagetest1.git
cd backstagetest1
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
# Install Python dependencies
pip install -r requirements.txt

# Install development dependencies
pip install -r requirements-dev.txt
```

### 4. Environment Configuration

Create a `.env` file in the project root:

```bash
# Copy the example environment file
cp .env.example .env
```

Edit the `.env` file with your configuration:

```env
DEBUG=True
SECRET_KEY=your-secret-key-here
DATABASE_URL=sqlite:///db.sqlite3
BACKSTAGE_URL=http://localhost:7007
```

### 5. Database Setup

```bash
# Run database migrations
python manage.py migrate

# Create a superuser (optional)
python manage.py createsuperuser
```

### 6. Collect Static Files

```bash
python manage.py collectstatic
```

### 7. Start Development Server

```bash
python manage.py runserver
```

The application will be available at `http://localhost:8000`.

## Verification

### Test the Installation

1. **Check Django Admin**: Visit `http://localhost:8000/admin/`
2. **Check API**: Visit `http://localhost:8000/api/`
3. **Check Health**: Visit `http://localhost:8000/health/`

### Run Tests

```bash
# Run all tests
python manage.py test

# Run with coverage
coverage run --source='.' manage.py test
coverage report
```

## Troubleshooting

### Common Issues

#### Python Version Issues
```bash
# Check Python version
python --version

# If using pyenv, set the correct version
pyenv local 3.11.0
```

#### Virtual Environment Issues
```bash
# If activation fails, recreate the environment
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

#### Database Issues
```bash
# Reset database
rm db.sqlite3
python manage.py migrate
```

#### Port Already in Use
```bash
# Use a different port
python manage.py runserver 8001
```

### Getting Help

If you encounter issues:

1. Check the [troubleshooting guide](../troubleshooting/common-issues.md)
2. Search [GitHub Issues](https://github.com/bateternal/backstagetest1/issues)
3. Ask in the #backstage-support Slack channel

## Next Steps

After successful installation:

1. Read the [Configuration Guide](configuration.md)
2. Follow the [First Steps](first-steps.md)
3. Explore the [API Documentation](../api/overview.md)

## Development Tools

### Recommended IDE Setup

#### VS Code
Install these extensions:
- Python
- Django
- GitLens
- REST Client

#### PyCharm
Configure Django support:
1. Go to Settings → Languages & Frameworks → Django
2. Enable Django support
3. Set Django project root to project directory

### Useful Commands

```bash
# Format code
black .

# Lint code
flake8 .

# Type checking
mypy .

# Run all checks
pre-commit run --all-files
```
