# Backstage Test Project

A comprehensive Django application integrated with Backstage for demonstrating various documentation patterns and best practices.

## 🚀 Quick Start

### Prerequisites

- Python 3.8+
- Django 5.2+
- Backstage instance (local or hosted)

### Installation

1. Clone the repository:
```bash
git clone https://github.com/bateternal/backstagetest1.git
cd backstagetest1
```

2. Create and activate virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Run database migrations:
```bash
python manage.py migrate
```

5. Start the development server:
```bash
python manage.py runserver
```

## 📚 Documentation Structure

This project serves as a comprehensive example of Backstage documentation patterns:

- **TechDocs**: Technical documentation using MkDocs
- **API Documentation**: REST API specifications and examples
- **Component Documentation**: Service-specific documentation
- **Deployment Guides**: Infrastructure and deployment instructions
- **Troubleshooting**: Common issues and solutions

## 🏗️ Project Architecture

```
backstagetest1/
├── backstagetest1/          # Django project configuration
│   ├── settings.py         # Django settings
│   ├── urls.py            # URL routing
│   └── wsgi.py            # WSGI configuration
├── docs/                   # TechDocs documentation
├── api/                    # API documentation
├── components/             # Component-specific docs
├── deployment/             # Deployment guides
├── troubleshooting/        # Troubleshooting guides
├── catalog-info.yaml       # Backstage catalog metadata
└── README.md              # This file
```

## 🔧 Configuration

### Backstage Integration

The project is configured to work with Backstage through the `catalog-info.yaml` file:

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: backstagetest1
  annotations:
    backstage.io/techdocs-ref: dir:.
spec:
  type: service
  lifecycle: production
  owner: smarties
```

### Environment Variables

Create a `.env` file for local development:

```bash
DEBUG=True
SECRET_KEY=your-secret-key-here
DATABASE_URL=sqlite:///db.sqlite3
```

## 🧪 Testing

Run the test suite:

```bash
python manage.py test
```

Run with coverage:

```bash
coverage run --source='.' manage.py test
coverage report
coverage html
```

## 📖 Available Documentation

- [Technical Documentation](docs/) - Comprehensive technical guides
- [API Reference](api/) - REST API documentation
- [Component Guides](components/) - Service-specific documentation
- [Deployment](deployment/) - Infrastructure and deployment guides
- [Troubleshooting](troubleshooting/) - Common issues and solutions

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 Support

- 📧 Email: support@example.com
- 💬 Slack: #backstage-support
- 📖 Documentation: [docs/](docs/)
- 🐛 Issues: [GitHub Issues](https://github.com/bateternal/backstagetest1/issues)

## 🔗 Links

- [Backstage Documentation](https://backstage.io/docs/)
- [Django Documentation](https://docs.djangoproject.com/)
- [Project Board](https://github.com/bateternal/backstagetest1/projects)
- [Releases](https://github.com/bateternal/backstagetest1/releases)
