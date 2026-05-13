# Claude Instructions for Backend

You are assisting with the backend part of the to-do application.

The backend provides the API, validation, business logic, database access, and migrations.

---

# Backend Stack

Use:

- Python
- FastAPI
- Pydantic
- SQLAlchemy
- Alembic
- PostgreSQL
- Docker Compose for local PostgreSQL
- pytest for backend tests later

---

# Backend Goal

The backend should provide a clean API for:

- user registration;
- creating todos;
- reading todos;
- updating todos;
- deleting todos.

The first version should stay simple.

Authentication can be added later as a separate feature.

---

# Suggested Backend Structure

Use this structure unless the existing project structure is different:

```text
backend/
├── app/
│   ├── main.py
│   ├── api/
│   │   └── routes/
│   │       ├── users.py
│   │       └── todos.py
│   ├── core/
│   │   └── config.py
│   ├── db/
│   │   ├── base.py
│   │   └── session.py
│   ├── models/
│   │   ├── user.py
│   │   └── todo.py
│   ├── schemas/
│   │   ├── user.py
│   │   └── todo.py
│   ├── services/
│   │   ├── user_service.py
│   │   └── todo_service.py
│   └── security/
│       └── passwords.py
├── alembic/
├── tests/
├── docker-compose.yml
├── .env
├── .env.example
└── pyproject.toml
```

If a different structure is used, explain why before changing it.


## Backend Layer Responsibilities

### FastAPI routes

Routes define API paths and HTTP methods.

Routes should:
- receive requests;
- use Pydantic schemas;
- call service functions;
- return responses;
- convert known service errors into HTTP errors.

Routes should not contain too much business logic.

### Pydantic schemas

Schemas define request and response shapes.

Schemas should:
- validate incoming request data;
- define response data;
- control what is returned to the frontend;
- prevent private fields from being returned.

For example, password_hash should never be part of a response schema.

### SQLAlchemy models

Models describe database tables.

Models should define:
- table names;
- columns;
- relationships;
- constraints;
- indexes where needed.

### Services

Services contain business logic.

Services should:
- check business rules;
- work with database sessions;
- create records;
- read records;
- update records;
- delete records;
- keep routes simple.

### Database session

The database session manages communication with PostgreSQL.

It is used to:
- execute queries;
- add records;
- commit changes;
- refresh records;
- roll back when needed.


## API Endpoints

First version endpoints:
```text
GET /health

POST /users/register

GET /todos
POST /todos
GET /todos/{todo_id}
PATCH /todos/{todo_id}
DELETE /todos/{todo_id}
```

For every endpoint, define:
- request schema;
- response schema;
- status code;
- possible errors;
- service function;
- database query;
- testing checklist.


## User Registration
```text
POST /users/register
```

Expected request:

```JSON
{
  "email": "user@example.com",
  "password": "password123"
}
```
Expected response:

```JSON
{
  "id": 1,
  "email": "user@example.com",
  "created_at": "2026-05-13T10:00:00"
}
```

Rules:
- email is required;
- email must have valid format;
- password is required;
- password must have a safe minimum length;
- email must be unique;
- password must be hashed before saving;
- password hash must not be returned.

Possible errors:
```text
400 Bad Request - invalid input
409 Conflict - email already exists
```


## Todo API

Expected todo fields:
- id
- user_id
- title
- description
- is_completed
- created_at
- updated_at

### Create todo
```text
POST /todos
```

Validate:
- title is required;
- title is not empty;
- title is not too long;
- description is optional;
- description is not too long;
- user exists or current user exists when authentication is added.

### Get todos
```text
GET /todos
```

Return a list of todos.

In the first version, this may return all todos.

Later, when authentication is added, it should return only todos for the current user.

### Update todo
```text
PATCH /todos/{todo_id}
```

Validate:
- todo exists;
- at least one field is provided;
- title is not empty if provided;
- title is not too long if provided;
- description is not too long if provided;
- is_completed is boolean if provided.

### Delete todo
```text
DELETE /todos/{todo_id}
```

Validate:
- todo exists;
- later: current authenticated user owns the todo.


## Validation Rules

Backend validation is required.

Do not rely only on frontend validation.

Use three validation layers:

1. Pydantic validation
Use Pydantic for:
- required fields;
- data types;
- email format;
- string length;
- optional fields;
- response shape.

Example:
```Python
from pydantic import BaseModel, EmailStr, Field


class UserCreate(BaseModel):
    email: EmailStr
    password: str = Field(min_length=8, max_length=128)
```

2. Service-level validation

Use service functions for business rules that require logic or database checks.

Examples:
- email already exists;
- todo does not exist;
- empty PATCH request;
- todo ownership when authentication is added.

3. Database constraints

Use database constraints for important stored data rules.

Examples:
- unique email;
- non-null fields;
- foreign keys;
- relationships.


## Database Rules

Use PostgreSQL as the local database.

Use Docker Compose to run PostgreSQL locally.

Do not introduce advanced Docker concepts unless needed.

Use SQLAlchemy models to represent database tables.

Use Alembic migrations to apply database structure changes.

Important rule:

Changing a SQLAlchemy model does not automatically change the database.

A migration is needed.


## SQLAlchemy Rules

When adding or changing a model, explain:
- what table it represents;
- what columns it creates;
- which fields are required;
- which fields are optional;
- which constraints exist;
- which relationships exist.

Prefer modern SQLAlchemy style with:
```Python
Mapped
mapped_column
```
unless the project already uses another style.


## Alembic Migration Rules

When creating or reviewing migrations, explain:
- what changed in the model;
- what changes in PostgreSQL;
- whether the migration is safe;
- what happens to existing rows;
- whether a default value is needed;
- whether a field should be nullable;
- how to test the migration.

Common commands may include:
```Bash
alembic revision --autogenerate -m "Create users and todos tables"
alembic upgrade head
```


## Query Reasoning Rules

When writing queries, explain:
- what data is needed;
- which table is queried;
- whether related data is needed;
- whether filtering is needed;
- whether sorting is needed;
- whether pagination is needed;
- what happens if no data is found.

Do not hide query logic behind ORM magic.

When using SQLAlchemy, explain the SQL idea behind the Python code.


## Error Handling Rules
Use clear HTTP errors.
Examples:
```text
400 Bad Request - invalid request data
404 Not Found - entity does not exist
409 Conflict - duplicate entity
500 Internal Server Error - unexpected server error
```

Known business errors should be converted to useful HTTP errors in routes.

Do not expose internal database errors directly to the frontend.


## Security Rules

Never store plain text passwords.

Always hash passwords before saving them.

Never return `password_hash`.

Do not commit `.env` files with real secrets.

Use `.env.example` for documentation.

Keep authentication out of the first version unless explicitly requested.

When authentication is added later, explain:
- password verification;
- token creation;
- protected routes;
- current user;
- ownership checks.


## Backend Testing
Manual API testing should start with FastAPI docs:
```text
http://localhost:8000/docs
```

Use it to test:
- GET;
- POST;
- PATCH;
- DELETE.

Later, automated tests can be added with:
- pytest;
- FastAPI TestClient;
- test database.

When testing an endpoint, check:
- expected status code;
- response body;
- validation errors;
- database changes;
- error cases.


## Backend Change Checklist

Before making backend changes, consider:
- Does this affect the API contract?
- Does this require a migration?
- Does this affect the frontend?
- Does this expose private data?
- Does this need validation?
- Does this need an error case?
- How can it be tested?

After making backend changes, explain:
- what changed;
- which files changed;
- whether migrations are needed;
- how to run or test the change.


## Final Backend Instruction

Keep the backend simple at first.

Prefer a clear working flow over advanced architecture.

First goal:
FastAPI starts
→ PostgreSQL runs locally
→ migration creates tables
→ registration works
→ todo CRUD works

