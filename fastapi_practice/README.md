# FastAPI Task Management Application

A comprehensive FastAPI learning project demonstrating user authentication, task CRUD operations, and role-based access control. Features JWT-based authentication, Argon2id password hashing, task ownership enforcement, and 49 comprehensive unit tests.

## Overview

This project builds a production-like task management API with:
- User authentication and authorization with JWT tokens
- Secure password hashing using Argon2id (OWASP recommended)
- Full CRUD operations for tasks with filtering, search, and pagination
- Role-based access control (user vs admin)
- Task ownership enforcement (users can only access their own tasks)
- 49 unit tests covering all major functionality
- Modular architecture with clear separation of concerns

## Tech Stack

- **FastAPI** 0.128.0 - Fast, modern web framework
- **Uvicorn** - ASGI server
- **Pydantic** 2.12.5 - Data validation and settings management
- **Argon2-cffi** - Secure password hashing
- **python-jose** - JWT token generation and validation
- **Pytest** 9.0.2 - Testing framework
- **httpx** 0.28.1 - Async HTTP client for testing
- **Python** 3.11+

## Installation

### Prerequisites

Before you begin, ensure you have the following installed:
- Python 3.11 or higher
- pip (Python package manager)

### Quick Start

#### 1. Clone Repository and Navigate

```bash
git clone <repository-url>
cd fastapi_practice
```

#### 2. Create Virtual Environment

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

#### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

#### 4. Create Environment File

Copy the example environment file and configure it:

```bash
cp .env.example .env
```

Edit `.env` file (optional - defaults are set in config.py):

```env
SECRET_KEY=your-secret-key-change-in-production
ACCESS_TOKEN_EXPIRE_MINUTES=30
ALGORITHM=HS256
DEBUG=True
```

#### 5. Run Application

```bash
# Option 1: Using uvicorn directly
uvicorn app.main:app --reload

# Option 2: Using run script (macOS/Linux)
./run.sh

# Option 3: Windows batch file
run.bat
```

#### 6. Access Application

- API Documentation (Swagger UI): http://localhost:8000/docs
- Alternative Docs (ReDoc): http://localhost:8000/redoc

## Project Structure

```
fastapi_practice/
├── app/                          # Main application package
│   ├── __init__.py              # Package initialization
│   ├── main.py                  # FastAPI app setup and configuration
│   │
│   ├── core/                    # Core utilities and configuration
│   │   ├── __init__.py
│   │   ├── config.py            # Settings and environment variables
│   │   ├── hashing.py           # Password hashing using Argon2id
│   │   ├── security.py          # JWT token generation and verification
│   │   ├── exceptions.py        # Custom exception classes
│   │   └── handlers.py          # Global exception handlers
│   │
│   ├── db/                      # Database layer
│   │   ├── __init__.py
│   │   └── storage.py           # In-memory storage (dictionaries)
│   │
│   ├── models/                  # Database/Domain models
│   │   └── __init__.py          # User, Task, Project models
│   │
│   ├── schemas/                 # Pydantic validation models
│   │   ├── __init__.py
│   │   ├── user.py             # User request/response schemas
│   │   ├── task.py             # Task schemas
│   │   ├── project.py          # Project schemas
│   │   └── query.py            # Query parameter schemas
│   │
│   ├── routers/                 # API route handlers
│   │   ├── __init__.py
│   │   ├── user.py             # /users endpoints (register, login, profile, update)
│   │   ├── task.py             # /tasks endpoints (CRUD, filtering, pagination)
│   │   └── project.py          # /projects endpoints (CRUD)
│   │
│   ├── services/                # Business logic layer
│   │   ├── __init__.py
│   │   ├── user_service.py     # User business logic
│   │   ├── task_service.py     # Task business logic
│   │   └── project_service.py  # Project business logic
│   │
│   └── dependencies/            # FastAPI dependency injection
│       ├── __init__.py
│       ├── user.py             # get_current_user, get_admin_user
│       └── task.py             # get_task_or_404
│
├── tests/                       # Test suite
│   ├── __init__.py
│   ├── conftest.py             # Pytest fixtures and configuration
│   ├── test_auth.py            # User authentication tests (19 tests)
│   ├── test_tasks.py           # Task CRUD tests (18 tests)
│   └── test_auth_errors.py     # Authorization and error handling tests (12 tests)
│
├── requirements.txt             # Python dependencies
├── .env.example                # Example environment variables
├── .gitignore                  # Git ignore rules
├── pyproject.toml              # Project metadata
├── pyrightconfig.json          # Pyright type checking config
├── run.sh                      # Start script for macOS/Linux
├── run.bat                     # Start script for Windows
└── README.md                   # This file
```

## API Endpoints

All authenticated endpoints require a JWT access token:
```
Authorization: Bearer <your-access-token>
```

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | /users/register | Register new user | ❌ |
| POST | /users/login | Login & get token | ❌ |
| GET | /users/me | Get current profile | ✅ |
| PUT | /users/me | Update profile | ✅ |
| POST | /tasks | Create task | ✅ |
| GET | /tasks | List my tasks | ✅ |
| GET | /tasks/{id} | Get task details | ✅ |
| PUT | /tasks/{id} | Update task | ✅ |
| DELETE | /tasks/{id} | Delete task | ✅ |
| POST | /projects | Create project | ✅ |
| GET | /projects | List my projects | ✅ |

**Query Parameters for Task List:**
- `?status=todo` - Filter by status (todo, in_progress, done)
- `?search=keyword` - Search by task title
- `?page=1` - Pagination (default: page 1)
- `?limit=10` - Items per page (default: 10)

### Example Requests

```bash
# Register User
POST /users/register
Body: {"email": "user@example.com", "password": "SecurePass123!"}

# Login
POST /users/login
Body: username=user@example.com&password=SecurePass123!

# Create Task
POST /tasks
Headers: Authorization: Bearer <access-token>
Body: {"title": "Learn FastAPI", "description": "Complete the tutorial", "status": "todo"}

# List Tasks
GET /tasks?status=todo&search=FastAPI&page=1&limit=10
Headers: Authorization: Bearer <access-token>
```

## Running Tests

```bash
# Run all tests
pytest tests/ -v

# Run specific test file
pytest tests/test_auth.py -v

# Run specific test
pytest tests/test_auth.py::TestRegister::test_register_success -v

# Generate coverage report
pytest tests/ --cov=app --cov-report=html
```

**Test Coverage** (49 tests):
- Authentication: 19 tests (Register, Login, Profile, Token Management)
- Task CRUD: 18 tests (Create, Read, Update, Delete, Filtering, Pagination)
- Authorization: 12 tests (Ownership, Permissions, Error Handling)

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `SECRET_KEY` | dev-key | JWT signing key (change in production) |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | 30 | Token expiry time in minutes |
| `ALGORITHM` | HS256 | JWT algorithm |
| `DEBUG` | False | Debug mode |

## Troubleshooting

### "Not authenticated" Error

**Problem**: Endpoints return 401 Unauthorized

**Solution**:
1. Register user: `POST /users/register`
2. Login: `POST /users/login` (receive access_token)
3. Use token in Authorization header: `Authorization: Bearer <token>`
4. Test endpoint again

### Token Expired

**Problem**: JWT token expired (usually after 30 minutes)

**Solution**: Login again to get a new access token

### Task Not Found (404)

**Problem**: Trying to access another user's task

**Solution**: Users can only access tasks they created. Verify you're accessing your own resources.

### Port Already in Use

**Problem**: Cannot run server on port 8000 (already in use)

**Solution**:
```bash
# Run on different port
uvicorn app.main:app --port 8001 --reload
```

## License

This is a learning project for educational purposes.
