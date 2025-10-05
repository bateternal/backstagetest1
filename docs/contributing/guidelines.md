# Contributing Guidelines

Thank you for your interest in contributing to the Backstage Test Project! This document provides guidelines for contributing to the project.

## Code of Conduct

By participating in this project, you agree to abide by our Code of Conduct:

- Be respectful and inclusive
- Welcome newcomers and help them learn
- Focus on constructive feedback
- Respect different viewpoints and experiences
- Show empathy towards other community members

## Getting Started

### Prerequisites

- Python 3.8+
- Git
- Basic knowledge of Django and Backstage
- Familiarity with Markdown for documentation

### Development Setup

1. **Fork and clone the repository:**
   ```bash
   git clone https://github.com/your-username/backstagetest1.git
   cd backstagetest1
   ```

2. **Create a virtual environment:**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   pip install -r requirements-dev.txt
   ```

4. **Set up pre-commit hooks:**
   ```bash
   pre-commit install
   ```

5. **Run tests to ensure everything works:**
   ```bash
   python manage.py test
   ```

## Contribution Types

### Bug Reports

When reporting bugs, please include:

- **Clear description** of the issue
- **Steps to reproduce** the problem
- **Expected behavior** vs actual behavior
- **Environment details** (OS, Python version, etc.)
- **Error messages** and logs
- **Screenshots** if applicable

Use the bug report template when creating GitHub issues.

### Feature Requests

For new features, please:

- **Describe the feature** and its benefits
- **Explain the use case** and motivation
- **Provide implementation ideas** if possible
- **Consider backward compatibility**
- **Discuss with maintainers** before implementation

### Code Contributions

#### Pull Request Process

1. **Create a feature branch:**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes:**
   - Write clean, readable code
   - Add tests for new functionality
   - Update documentation as needed
   - Follow the coding standards

3. **Test your changes:**
   ```bash
   python manage.py test
   coverage run --source='.' manage.py test
   coverage report
   ```

4. **Run code quality checks:**
   ```bash
   flake8 .
   black --check .
   isort --check-only .
   mypy .
   ```

5. **Commit your changes:**
   ```bash
   git add .
   git commit -m "feat: add your feature description"
   ```

6. **Push and create a pull request:**
   ```bash
   git push origin feature/your-feature-name
   ```

#### Commit Message Format

Use conventional commit messages:

```
type(scope): description

[optional body]

[optional footer]
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes
- `refactor`: Code refactoring
- `test`: Test additions/changes
- `chore`: Maintenance tasks

**Examples:**
```
feat(api): add user authentication endpoint
fix(docs): correct installation instructions
docs(readme): update project description
```

### Documentation Contributions

#### Documentation Standards

- Use clear, concise language
- Include code examples
- Add diagrams for complex concepts
- Keep documentation up-to-date
- Follow the existing structure

#### Documentation Types

1. **Technical Documentation** (`docs/`):
   - Architecture guides
   - API documentation
   - Configuration guides
   - Troubleshooting guides

2. **Code Documentation**:
   - Docstrings for functions and classes
   - Inline comments for complex logic
   - Type hints for better IDE support

3. **README Updates**:
   - Project description
   - Installation instructions
   - Usage examples
   - Contributing guidelines

## Coding Standards

### Python Code

Follow these standards:

1. **PEP 8** style guidelines
2. **Type hints** for function signatures
3. **Docstrings** for all public functions/classes
4. **Meaningful variable names**
5. **Single responsibility principle**

**Example:**
```python
from typing import List, Optional

def get_user_projects(user_id: int, status: Optional[str] = None) -> List[dict]:
    """
    Retrieve projects for a specific user.
    
    Args:
        user_id: The ID of the user
        status: Optional status filter
        
    Returns:
        List of project dictionaries
        
    Raises:
        UserNotFound: If user doesn't exist
    """
    # Implementation here
    pass
```

### Django Code

Follow Django best practices:

1. **Model naming**: Use singular nouns
2. **View organization**: Use class-based views when appropriate
3. **URL patterns**: Use descriptive names
4. **Template structure**: Keep templates simple and reusable
5. **Form handling**: Use Django forms for validation

**Example:**
```python
# models.py
class Project(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        ordering = ['-created_at']
    
    def __str__(self):
        return self.name

# views.py
class ProjectListView(ListView):
    model = Project
    template_name = 'projects/project_list.html'
    context_object_name = 'projects'
    paginate_by = 20
```

### Testing Standards

Write comprehensive tests:

1. **Unit tests** for individual functions
2. **Integration tests** for API endpoints
3. **Model tests** for database operations
4. **View tests** for user interactions
5. **Edge cases** and error conditions

**Example:**
```python
# tests.py
from django.test import TestCase
from django.urls import reverse
from rest_framework.test import APITestCase
from rest_framework import status

class ProjectModelTest(TestCase):
    def test_project_creation(self):
        project = Project.objects.create(
            name='Test Project',
            description='Test description'
        )
        self.assertEqual(project.name, 'Test Project')
        self.assertTrue(project.created_at)

class ProjectAPITest(APITestCase):
    def setUp(self):
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass123'
        )
        self.client.force_authenticate(user=self.user)
    
    def test_create_project(self):
        data = {
            'name': 'API Test Project',
            'description': 'Created via API'
        }
        response = self.client.post('/api/v1/projects/', data)
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
```

## Review Process

### Pull Request Review

All pull requests go through review:

1. **Automated checks** must pass
2. **Code review** by maintainers
3. **Documentation review** if applicable
4. **Testing** of changes
5. **Approval** from maintainers

### Review Criteria

Reviewers will check:

- **Code quality** and adherence to standards
- **Test coverage** for new functionality
- **Documentation updates** as needed
- **Backward compatibility** considerations
- **Performance impact** assessment
- **Security implications** review

### Responding to Feedback

When receiving feedback:

1. **Be open** to suggestions
2. **Ask questions** if unclear
3. **Make requested changes** promptly
4. **Respond to comments** professionally
5. **Learn from the process**

## Release Process

### Versioning

We use semantic versioning (SemVer):
- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes (backward compatible)

### Release Schedule

- **Patch releases**: As needed for bug fixes
- **Minor releases**: Monthly for new features
- **Major releases**: Quarterly for significant changes

### Release Checklist

Before each release:

- [ ] All tests pass
- [ ] Documentation is updated
- [ ] Changelog is updated
- [ ] Version numbers are bumped
- [ ] Release notes are prepared
- [ ] Security review is completed

## Community Guidelines

### Communication

- **Be respectful** in all interactions
- **Use inclusive language** in code and documentation
- **Provide constructive feedback**
- **Help newcomers** learn and contribute
- **Share knowledge** and best practices

### Getting Help

If you need help:

1. **Check documentation** first
2. **Search existing issues** for similar problems
3. **Ask questions** in GitHub discussions
4. **Join community Slack** for real-time help
5. **Attend community meetups**

### Recognition

Contributors are recognized through:

- **Contributor list** in README
- **Release notes** acknowledgments
- **Community highlights** in newsletters
- **Speaking opportunities** at events

## Development Tools

### Recommended IDE Setup

**VS Code:**
```json
{
    "python.defaultInterpreterPath": "./venv/bin/python",
    "python.linting.enabled": true,
    "python.linting.flake8Enabled": true,
    "python.formatting.provider": "black",
    "python.sortImports.args": ["--profile", "black"]
}
```

**PyCharm:**
- Enable Django support
- Configure Python interpreter to use virtual environment
- Install plugins: Django, Git, Markdown

### Useful Commands

```bash
# Development
python manage.py runserver
python manage.py shell
python manage.py dbshell

# Testing
python manage.py test
coverage run --source='.' manage.py test
coverage html

# Code Quality
flake8 .
black .
isort .
mypy .

# Documentation
mkdocs serve
mkdocs build

# Database
python manage.py makemigrations
python manage.py migrate
python manage.py showmigrations
```

## License

By contributing to this project, you agree that your contributions will be licensed under the MIT License.

## Questions?

If you have questions about contributing:

- **GitHub Discussions**: For general questions
- **Slack**: #contributing channel
- **Email**: contributors@example.com
- **Issues**: Create a GitHub issue

Thank you for contributing to the Backstage Test Project!
