# Common Issues and Solutions

This guide covers the most frequently encountered issues when working with the Backstage Test Project and their solutions.

## Installation Issues

### Python Version Problems

**Problem:** `python: command not found` or incorrect Python version

**Symptoms:**
- Installation fails with Python version errors
- `python --version` shows wrong version

**Solutions:**

1. **Install Python 3.11+ using pyenv:**
   ```bash
   # Install pyenv
   curl https://pyenv.run | bash
   
   # Add to shell profile
   echo 'export PATH="$HOME/.pyenv/bin:$PATH"' >> ~/.bashrc
   echo 'eval "$(pyenv init -)"' >> ~/.bashrc
   
   # Install Python 3.11
   pyenv install 3.11.0
   pyenv local 3.11.0
   ```

2. **Use system package manager:**
   ```bash
   # Ubuntu/Debian
   sudo apt update
   sudo apt install python3.11 python3.11-venv python3.11-pip
   
   # macOS with Homebrew
   brew install python@3.11
   ```

3. **Verify installation:**
   ```bash
   python3.11 --version
   which python3.11
   ```

### Virtual Environment Issues

**Problem:** Virtual environment activation fails or packages not found

**Symptoms:**
- `source venv/bin/activate` fails
- `pip install` installs to system Python
- Import errors after activation

**Solutions:**

1. **Recreate virtual environment:**
   ```bash
   rm -rf venv
   python3.11 -m venv venv
   source venv/bin/activate
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

2. **Check activation:**
   ```bash
   which python
   which pip
   # Should show paths inside venv directory
   ```

3. **Windows-specific issues:**
   ```cmd
   # Use PowerShell or Command Prompt
   venv\Scripts\activate.bat
   # or
   venv\Scripts\Activate.ps1
   ```

### Dependency Installation Failures

**Problem:** `pip install` fails with compilation errors

**Symptoms:**
- Compilation errors for native extensions
- Missing system dependencies
- SSL certificate errors

**Solutions:**

1. **Install system dependencies:**
   ```bash
   # Ubuntu/Debian
   sudo apt install build-essential libssl-dev libffi-dev python3-dev
   
   # macOS
   xcode-select --install
   brew install openssl libffi
   ```

2. **Use pre-compiled wheels:**
   ```bash
   pip install --only-binary=all -r requirements.txt
   ```

3. **Fix SSL issues:**
   ```bash
   pip install --trusted-host pypi.org --trusted-host pypi.python.org --trusted-host files.pythonhosted.org -r requirements.txt
   ```

## Database Issues

### Migration Failures

**Problem:** Database migrations fail or database is locked

**Symptoms:**
- `python manage.py migrate` fails
- Database locked errors
- Table already exists errors

**Solutions:**

1. **Reset migrations (development only):**
   ```bash
   # Delete migration files
   find . -path "*/migrations/*.py" -not -name "__init__.py" -delete
   find . -path "*/migrations/*.pyc" -delete
   
   # Recreate migrations
   python manage.py makemigrations
   python manage.py migrate
   ```

2. **Handle locked database:**
   ```bash
   # For SQLite
   rm db.sqlite3
   python manage.py migrate
   
   # For PostgreSQL
   sudo -u postgres psql -c "DROP DATABASE IF EXISTS backstagetest1;"
   sudo -u postgres psql -c "CREATE DATABASE backstagetest1;"
   python manage.py migrate
   ```

3. **Check database permissions:**
   ```bash
   # PostgreSQL
   sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE backstagetest1 TO your_user;"
   ```

### Connection Issues

**Problem:** Cannot connect to database

**Symptoms:**
- `django.db.utils.OperationalError`
- Connection timeout errors
- Authentication failures

**Solutions:**

1. **Check database configuration:**
   ```python
   # settings.py
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.postgresql',
           'NAME': 'backstagetest1',
           'USER': 'your_user',
           'PASSWORD': 'your_password',
           'HOST': 'localhost',
           'PORT': '5432',
       }
   }
   ```

2. **Test database connection:**
   ```bash
   # PostgreSQL
   psql -h localhost -U your_user -d backstagetest1
   
   # MySQL
   mysql -h localhost -u your_user -p backstagetest1
   ```

3. **Check database service:**
   ```bash
   # PostgreSQL
   sudo systemctl status postgresql
   sudo systemctl start postgresql
   
   # MySQL
   sudo systemctl status mysql
   sudo systemctl start mysql
   ```

## Server Issues

### Port Already in Use

**Problem:** `python manage.py runserver` fails with port in use error

**Symptoms:**
- `Error: That port is already in use`
- Server won't start

**Solutions:**

1. **Use different port:**
   ```bash
   python manage.py runserver 8001
   ```

2. **Find and kill process using port:**
   ```bash
   # Find process using port 8000
   lsof -i :8000
   
   # Kill the process
   kill -9 <PID>
   ```

3. **Use system port scanner:**
   ```bash
   # Linux
   netstat -tulpn | grep :8000
   
   # macOS
   lsof -i :8000
   ```

### Static Files Not Loading

**Problem:** CSS, JavaScript, and images not loading

**Symptoms:**
- Broken styling
- JavaScript errors
- 404 errors for static files

**Solutions:**

1. **Collect static files:**
   ```bash
   python manage.py collectstatic
   ```

2. **Check static files configuration:**
   ```python
   # settings.py
   STATIC_URL = '/static/'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   STATICFILES_DIRS = [
       os.path.join(BASE_DIR, 'static'),
   ]
   ```

3. **Serve static files in development:**
   ```python
   # urls.py
   from django.conf import settings
   from django.conf.urls.static import static
   
   if settings.DEBUG:
       urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
   ```

### Memory Issues

**Problem:** Application runs out of memory

**Symptoms:**
- `MemoryError` exceptions
- Slow performance
- System becomes unresponsive

**Solutions:**

1. **Increase Python memory limit:**
   ```bash
   export PYTHONHASHSEED=0
   export PYTHONMALLOC=malloc
   ```

2. **Optimize Django settings:**
   ```python
   # settings.py
   DEBUG = False  # Disable debug mode
   
   # Use database connection pooling
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.postgresql',
           'OPTIONS': {
               'MAX_CONNS': 20,
           }
       }
   }
   ```

3. **Monitor memory usage:**
   ```bash
   # Install memory profiler
   pip install memory-profiler
   
   # Profile memory usage
   python -m memory_profiler manage.py runserver
   ```

## API Issues

### Authentication Failures

**Problem:** API requests return 401 Unauthorized

**Symptoms:**
- `{"detail": "Authentication credentials were not provided."}`
- Token validation errors

**Solutions:**

1. **Check token format:**
   ```bash
   # Correct format
   curl -H "Authorization: Bearer your-token-here" http://localhost:8000/api/v1/users
   
   # Wrong format
   curl -H "Authorization: your-token-here" http://localhost:8000/api/v1/users
   ```

2. **Verify token validity:**
   ```python
   # Check token in Django shell
   python manage.py shell
   >>> from rest_framework.authtoken.models import Token
   >>> Token.objects.filter(key='your-token-here').exists()
   ```

3. **Generate new token:**
   ```python
   # Create new token for user
   from django.contrib.auth.models import User
   from rest_framework.authtoken.models import Token
   
   user = User.objects.get(username='your_username')
   token, created = Token.objects.get_or_create(user=user)
   print(token.key)
   ```

### CORS Issues

**Problem:** Cross-origin requests blocked

**Symptoms:**
- `Access to XMLHttpRequest has been blocked by CORS policy`
- Browser console errors

**Solutions:**

1. **Install django-cors-headers:**
   ```bash
   pip install django-cors-headers
   ```

2. **Configure CORS settings:**
   ```python
   # settings.py
   INSTALLED_APPS = [
       'corsheaders',
       # ... other apps
   ]
   
   MIDDLEWARE = [
       'corsheaders.middleware.CorsMiddleware',
       # ... other middleware
   ]
   
   CORS_ALLOWED_ORIGINS = [
       "http://localhost:3000",
       "http://127.0.0.1:3000",
   ]
   
   CORS_ALLOW_CREDENTIALS = True
   ```

3. **Test CORS configuration:**
   ```bash
   curl -H "Origin: http://localhost:3000" \
        -H "Access-Control-Request-Method: POST" \
        -H "Access-Control-Request-Headers: X-Requested-With" \
        -X OPTIONS \
        http://localhost:8000/api/v1/users
   ```

## Performance Issues

### Slow Database Queries

**Problem:** Application responds slowly due to database queries

**Symptoms:**
- Long response times
- High database CPU usage
- Timeout errors

**Solutions:**

1. **Enable query logging:**
   ```python
   # settings.py
   LOGGING = {
       'version': 1,
       'disable_existing_loggers': False,
       'handlers': {
           'console': {
               'class': 'logging.StreamHandler',
           },
       },
       'loggers': {
           'django.db.backends': {
               'handlers': ['console'],
               'level': 'DEBUG',
           },
       },
   }
   ```

2. **Add database indexes:**
   ```python
   # models.py
   class User(models.Model):
       email = models.EmailField(db_index=True)
       created_at = models.DateTimeField(auto_now_add=True, db_index=True)
   ```

3. **Use select_related and prefetch_related:**
   ```python
   # Optimize queries
   users = User.objects.select_related('profile').prefetch_related('posts')
   ```

### High Memory Usage

**Problem:** Application consumes too much memory

**Symptoms:**
- System becomes slow
- Out of memory errors
- High memory usage in monitoring

**Solutions:**

1. **Enable database connection pooling:**
   ```python
   # settings.py
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.postgresql',
           'OPTIONS': {
               'MAX_CONNS': 10,
           }
       }
   }
   ```

2. **Use caching:**
   ```python
   # settings.py
   CACHES = {
       'default': {
           'BACKEND': 'django.core.cache.backends.redis.RedisCache',
           'LOCATION': 'redis://127.0.0.1:6379/1',
       }
   }
   ```

3. **Optimize static file serving:**
   ```python
   # Use CDN for static files
   STATIC_URL = 'https://cdn.example.com/static/'
   ```

## Getting Help

### Debugging Steps

1. **Enable debug mode:**
   ```python
   DEBUG = True
   ```

2. **Check logs:**
   ```bash
   tail -f logs/django.log
   ```

3. **Use Django debug toolbar:**
   ```bash
   pip install django-debug-toolbar
   ```

### Support Channels

- 📧 **Email**: support@example.com
- 💬 **Slack**: #backstage-support
- 🐛 **GitHub Issues**: [Create an issue](https://github.com/bateternal/backstagetest1/issues)
- 📖 **Documentation**: Check other sections of this documentation

### Useful Commands

```bash
# Check Django configuration
python manage.py check

# Show URL patterns
python manage.py show_urls

# Database shell
python manage.py dbshell

# Django shell
python manage.py shell

# Clear cache
python manage.py clear_cache

# Show migrations
python manage.py showmigrations
```

## Next Steps

- [Error Codes Reference](error-codes.md)
- [FAQ](faq.md)
- [Performance Optimization Guide](../development/debugging.md)
