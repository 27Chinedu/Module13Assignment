# FastAPI Calculator Application with JWT Authentication

A production-ready FastAPI application featuring JWT-based user authentication, calculation management with full CRUD operations, PostgreSQL database integration, and automated CI/CD deployment pipeline.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Running the Application](#running-the-application)
- [Authentication](#authentication)
- [API Endpoints](#api-endpoints)
- [Front-End Pages](#front-end-pages)
- [Testing](#testing)
- [CI/CD Pipeline](#cicd-pipeline)
- [Docker Deployment](#docker-deployment)
- [Development Guidelines](#development-guidelines)
- [Troubleshooting](#troubleshooting)

## Overview

This project implements a comprehensive FastAPI-based calculator application with JWT authentication and complete BREAD (Browse, Read, Edit, Add, Delete) operations for calculations. It demonstrates advanced software engineering practices including secure authentication, polymorphic database models, factory patterns, comprehensive testing strategies including Playwright E2E tests, and automated CI/CD deployment to Docker Hub.

The application supports user registration and login with secure password hashing, four arithmetic operations (addition, subtraction, multiplication, division) with database persistence, and user-specific calculation history management through a responsive web interface.

## Features

- **JWT Authentication**: Secure token-based authentication with access and refresh tokens
- **User Management**: Complete registration and login with password validation
- **Password Security**: Bcrypt hashing with configurable rounds
- **Calculation Operations**: Addition, subtraction, multiplication, and division
- **BREAD Operations**: Full create, read, update, delete functionality for calculations
- **RESTful API**: Clean, documented endpoints following REST principles
- **Front-End Interface**: HTML/CSS/JavaScript pages with Tailwind CSS styling
- **Database Persistence**: SQLAlchemy ORM with PostgreSQL
- **Polymorphic Models**: Elegant calculation hierarchy using inheritance
- **Factory Pattern**: Clean object creation for different calculation types
- **Pydantic Validation**: Comprehensive request/response validation
- **Multi-Layer Testing**: Unit, integration, and Playwright E2E tests
- **CI/CD Pipeline**: Automated testing, security scanning, and deployment
- **Interactive Documentation**: Auto-generated OpenAPI (Swagger) documentation
- **Production-Ready**: Docker containerization with health checks

## Technology Stack

- **Framework**: FastAPI 0.115.8
- **Database**: PostgreSQL 17
- **ORM**: SQLAlchemy 2.0.38
- **Authentication**: python-jose (JWT), passlib (bcrypt)
- **Validation**: Pydantic 2.10.6
- **Templates**: Jinja2 3.1.5
- **Testing**: pytest 8.3.4, pytest-cov 6.0.0, Playwright 1.50.0
- **Web Server**: Uvicorn 0.34.0
- **Containerization**: Docker with multi-platform builds
- **CI/CD**: GitHub Actions
- **Security**: Trivy vulnerability scanner

## Project Structure

```
.
├── .github/
│   └── workflows/
│       └── python-app.yml          # CI/CD pipeline configuration
├── app/
│   ├── auth/
│   │   ├── dependencies.py         # Authentication dependencies
│   │   ├── jwt.py                  # JWT token handling
│   │   └── redis.py                # Token blacklisting (optional)
│   ├── core/
│   │   └── config.py               # Application settings
│   ├── models/
│   │   ├── calculation.py          # Polymorphic calculation models
│   │   └── user.py                 # User model with authentication
│   ├── operations/
│   │   └── __init__.py             # Arithmetic operations
│   ├── schemas/
│   │   ├── calculation.py          # Calculation validation schemas
│   │   ├── token.py                # Token schemas
│   │   └── user.py                 # User validation schemas
│   ├── database.py                 # Database configuration
│   ├── database_init.py            # Database initialization
│   └── main.py                     # Application entry point
├── static/
│   ├── css/
│   │   └── style.css               # Application styles
│   └── js/
│       └── script.js               # Client-side JavaScript
├── templates/
│   ├── layout.html                 # Base template
│   ├── index.html                  # Home page
│   ├── login.html                  # Login page
│   ├── register.html               # Registration page
│   └── dashboard.html              # User dashboard
├── tests/
│   ├── unit/                       # Unit tests
│   ├── integration/                # Integration tests
│   ├── e2e/                        # Playwright E2E tests
│   └── conftest.py                 # Test fixtures
├── Dockerfile                      # Production container
├── docker-compose.yml              # Local development setup
├── requirements.txt                # Python dependencies
├── pytest.ini                      # Test configuration
└── README.md                       # This file
```

## Installation

### Prerequisites

- Python 3.10 or higher
- PostgreSQL 13 or higher (or Docker)
- Docker and Docker Compose (recommended)
- Git

### Local Setup

1. Clone the repository:
```bash
git clone <repository-url>
cd <repository-name>
```

2. Create and activate a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Install Playwright browsers (for E2E tests):
```bash
playwright install
```

5. Set up environment variables (create `.env` file):
```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/fastapi_db
JWT_SECRET_KEY=your-super-secret-key-change-in-production
JWT_REFRESH_SECRET_KEY=your-refresh-secret-key
ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7
BCRYPT_ROUNDS=12
```

## Running the Application

### Using Docker Compose (Recommended)

```bash
docker-compose up
```

This starts:
- FastAPI application on `http://localhost:8000`
- PostgreSQL database on port 5432
- pgAdmin on `http://localhost:5050`

Access credentials for pgAdmin:
- Email: `admin@example.com`
- Password: `admin`

### Local Development

1. Start PostgreSQL (if not using Docker)

2. Initialize the database:
```bash
python -m app.database_init
```

3. Run the application:
```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

4. Access the application:
- Home Page: `http://localhost:8000/`
- Login: `http://localhost:8000/login`
- Register: `http://localhost:8000/register`
- Dashboard: `http://localhost:8000/dashboard`
- API Documentation: `http://localhost:8000/docs`
- Alternative Docs: `http://localhost:8000/redoc`

## Authentication

### JWT Token System

The application uses JWT (JSON Web Tokens) for stateless authentication:

**Access Token:**
- Short-lived (30 minutes default)
- Used for API authentication
- Required for all calculation endpoints

**Refresh Token:**
- Long-lived (7 days default)
- Used to obtain new access tokens
- Stored securely by client in localStorage

### Password Security

- **Hashing Algorithm**: bcrypt with configurable rounds (default: 12)
- **Password Requirements**:
  - Minimum 8 characters
  - At least one uppercase letter
  - At least one lowercase letter
  - At least one digit
  - At least one special character

### Token Storage

Tokens are stored in the browser's localStorage and included in API requests via the Authorization header:

```javascript
localStorage.setItem('access_token', token);
// Used in requests as:
headers: { 'Authorization': `Bearer ${token}` }
```

## API Endpoints

### Authentication Endpoints

#### Register User
```http
POST /auth/register
Content-Type: application/json

{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john.doe@example.com",
  "username": "johndoe",
  "password": "SecurePass123!",
  "confirm_password": "SecurePass123!"
}
```

**Response (201):**
```json
{
  "id": "uuid",
  "username": "johndoe",
  "email": "john.doe@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "is_active": true,
  "is_verified": false,
  "created_at": "2025-01-01T00:00:00",
  "updated_at": "2025-01-01T00:00:00"
}
```

#### Login
```http
POST /auth/login
Content-Type: application/json

{
  "username": "johndoe",
  "password": "SecurePass123!"
}
```

**Response (200):**
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "token_type": "bearer",
  "expires_at": "2025-01-01T00:30:00",
  "user_id": "uuid",
  "username": "johndoe",
  "email": "john.doe@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "is_active": true,
  "is_verified": false
}
```

### Calculation Endpoints (BREAD)

All calculation endpoints require authentication via Bearer token.

#### Create Calculation
```http
POST /calculations
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "type": "addition",
  "inputs": [10.5, 3, 2]
}
```

#### List Calculations
```http
GET /calculations
Authorization: Bearer <access_token>
```

#### Get Calculation
```http
GET /calculations/{calculation_id}
Authorization: Bearer <access_token>
```

#### Update Calculation
```http
PUT /calculations/{calculation_id}
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "inputs": [42, 7]
}
```

#### Delete Calculation
```http
DELETE /calculations/{calculation_id}
Authorization: Bearer <access_token>
```

## Front-End Pages

### Registration Page (`/register`)

Features:
- Client-side validation for email format
- Password strength requirements enforced
- Password confirmation matching
- Real-time validation feedback
- Error and success message display

### Login Page (`/login`)

Features:
- Username/email and password fields
- Remember me functionality
- Token storage in localStorage
- Redirect to dashboard on success
- Error message display for invalid credentials

### Dashboard Page (`/dashboard`)

Features:
- Create new calculations with type selection
- View calculation history in a table
- Delete calculations
- User welcome message
- Automatic token validation and refresh
- Logout functionality

## Testing

### Test Structure

```
tests/
├── unit/                      # Unit tests for operations
│   └── test_calculator.py
├── integration/               # Integration tests
│   ├── test_calculation.py
│   ├── test_user.py
│   └── test_user_auth.py
└── e2e/                       # Playwright E2E tests
    └── test_fastapi_calculator.py
```

### Running Tests Locally

**All Tests:**
```bash
pytest
```

**With Coverage:**
```bash
pytest --cov=app --cov-report=html
```

**Specific Test Categories:**
```bash
pytest tests/unit/                  # Unit tests
pytest tests/integration/           # Integration tests
pytest tests/e2e/                   # E2E tests
```

**View Coverage Report:**
```bash
open htmlcov/index.html  # macOS
# or
start htmlcov/index.html  # Windows
```

### Playwright E2E Tests

The E2E tests cover complete user workflows:

**Positive Tests:**
1. User registration with valid data
2. User login with correct credentials
3. Creating calculations of all types
4. Listing and viewing calculations
5. Updating calculations
6. Deleting calculations

**Negative Tests:**
1. Registration with duplicate email/username
2. Login with invalid credentials
3. Calculation operations without authentication
4. Invalid calculation inputs (division by zero, etc.)

Run Playwright tests:
```bash
pytest tests/e2e/ -v
```

### Test Database Setup

Tests use a separate test database configured in `conftest.py`:
- Automatically created before tests
- Cleaned between test runs
- Uses fixtures for isolated test data

## CI/CD Pipeline

### GitHub Actions Workflow

The pipeline consists of three jobs:

#### 1. Test Job

**Services:**
- PostgreSQL with health checks

**Steps:**
1. Checkout code
2. Setup Python 3.10
3. Cache dependencies
4. Install requirements and Playwright
5. Run unit tests with coverage
6. Run integration tests
7. Run E2E tests

#### 2. Security Job

**Steps:**
1. Build Docker image
2. Run Trivy vulnerability scanner
3. Check for CRITICAL and HIGH vulnerabilities
4. Exit on findings (unless ignored in `.trivyignore`)

#### 3. Deploy Job

**Conditions:**
- Only runs on `main` branch
- Requires test and security jobs to pass
- Uses `production` environment

**Steps:**
1. Setup Docker Buildx
2. Login to Docker Hub
3. Build multi-platform image (`linux/amd64`, `linux/arm64`)
4. Push with tags:
   - `latest`
   - Git commit SHA

### Setting Up CI/CD

1. **Configure GitHub Secrets:**

Navigate to repository Settings > Secrets and variables > Actions:

```
DOCKERHUB_USERNAME: your-dockerhub-username
DOCKERHUB_TOKEN: your-dockerhub-access-token
```

2. **Create Production Environment:**

Settings > Environments > New environment: `production`

3. **Update Docker Hub Repository:**

In `.github/workflows/python-app.yml`, update the image tags:
```yaml
tags: |
  your-username/your-repo:latest
  your-username/your-repo:${{ github.sha }}
```

### Monitoring Pipeline

- View runs in the Actions tab
- Click on individual runs for detailed logs
- Failed jobs show red X with error details
- Successful deployments show green checkmark

## Docker Deployment

### Docker Hub Repository

**Image:** `cerechukwu27/module13:latest`

### Pulling and Running

```bash
# Pull latest image
docker pull cerechukwu27/module13:latest

# Run with environment variables
docker run -p 8000:8000 \
  -e DATABASE_URL=postgresql://user:pass@host:5432/db \
  -e JWT_SECRET_KEY=your-secret-key \
  -e JWT_REFRESH_SECRET_KEY=your-refresh-secret \
  cerechukwu27/module13:latest
```

### Using Docker Compose

```yaml
version: '3.8'
services:
  app:
    image: cerechukwu27/module13:latest
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://postgres:postgres@db:5432/fastapi_db
      JWT_SECRET_KEY: your-secret-key
      JWT_REFRESH_SECRET_KEY: your-refresh-secret
    depends_on:
      - db
  
  db:
    image: postgres:17
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: fastapi_db
```

### Health Check

The Docker image includes a health check endpoint:

```bash
curl http://localhost:8000/health
```

Expected response:
```json
{"status": "ok"}
```

## Development Guidelines

### Adding New Features

**New Calculation Type:**
1. Add subclass in `app/models/calculation.py`
2. Implement `get_result()` method
3. Add to factory dictionary
4. Update `CalculationType` enum in schemas
5. Write tests

**New Front-End Page:**
1. Create HTML template in `templates/`
2. Extend `layout.html` base template
3. Add route in `app/main.py`
4. Add navigation link
5. Style with Tailwind CSS classes

### Code Style

- Follow PEP 8 guidelines
- Use type hints for function parameters and returns
- Write docstrings for all public functions
- Keep functions focused on single responsibility
- Use meaningful variable and function names

### Security Considerations

- Never commit secrets to repository
- Use environment variables for configuration
- Validate all user inputs with Pydantic schemas
- Use parameterized queries (SQLAlchemy handles this)
- Keep dependencies updated regularly
- Review security scan results from Trivy
- Rotate JWT secrets in production
- Use HTTPS in production environments

## Troubleshooting

### Common Issues

**Authentication Errors:**
```
401 Unauthorized: Could not validate credentials
```
- Check token hasn't expired
- Verify Bearer token format in Authorization header
- Ensure JWT_SECRET_KEY is consistent across restarts

**Database Connection:**
```
Could not connect to database
```
- Verify PostgreSQL is running
- Check DATABASE_URL is correct
- Ensure database exists and is accessible

**Front-End Token Issues:**
```
Token not being sent with requests
```
- Check token is stored in localStorage
- Verify Authorization header format
- Check browser console for JavaScript errors

**Tests Failing:**
```
Database fixtures not working
```
- Check PostgreSQL service in tests
- Verify test database creation in conftest.py
- Review database connection settings

**Docker Build Fails:**
```
Cannot install requirements
```
- Check Python version in Dockerfile matches requirements
- Verify requirements.txt is complete and valid
- Review build logs for specific package errors

**Playwright Tests Timing Out:**
```
Tests fail with timeout errors
```
- Increase timeout in playwright config
- Check FastAPI server is starting correctly
- Verify database is accessible during tests

### Getting Help

1. Check application logs: `docker-compose logs web`
2. Review test output with verbose flag: `pytest -v`
3. Verify database state via pgAdmin
4. Check GitHub Actions logs for CI/CD issues
5. Review FastAPI documentation: https://fastapi.tiangolo.com/
6. Check Playwright documentation: https://playwright.dev/python/
