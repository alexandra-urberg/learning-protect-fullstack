# Claude Instructions for To-Do Project

You are assisting with a full-stack to-do application.

This project contains two separate parts:

```text
project-root/
├── frontend/
└── backend/
```

The frontend and backend should stay separated, but they should work together through API requests.

## Project Overview
The application is a to-do list app with simple user registration.

The app should allow a user to:
- register;
- create to-do items;
- view to-do items;
- update to-do items;
- mark to-do items as completed or not completed;
- delete to-do items.

The project should start small and grow only when there is a clear reason.

## Main Product Flow

The core flow is:
Frontend → API request → Backend logic → Database → Backend response → UI update

For every feature, consider:
- what the user does in the UI;
- what request the frontend sends;
- what the backend validates;
- what data is stored or changed;
- what response the frontend receives;
- how the UI updates.

## Project Boundaries

The frontend and backend have separate responsibilities.

### Frontend responsibilities

The frontend is responsible for:
- rendering UI;
- collecting user input;
- showing loading states;
- showing empty states;
- showing error states;
- sending API requests;
- updating UI after backend responses;
- doing user-friendly form validation.


### Backend responsibilities

The backend is responsible for:
- exposing API endpoints;
- validating request data;
- applying business rules;
- reading and writing database data;
- returning safe response data;
- handling errors correctly;
- protecting stored data rules.

### Database responsibilities

The database is responsible for:
- storing users;
- storing to-do items;
- enforcing important constraints;
- preserving relationships between entities.


## General Development Rules

Start with the simplest working version.

Avoid adding advanced architecture before it is needed.

Prefer small, understandable changes.

Before making large changes, explain:
- what should change;
- why it should change;
- which part of the project is affected;
- how to test the change.

Do not rewrite many files without explaining the plan first.

Do not delete files unless explicitly asked.

After code changes, summarize:
- what changed;
- why it changed;
- how to test it.


## Main Entities
### User
Represents a person who can create to-do items.

Expected fields:
- id
- email
- password_hash
- created_at
- updated_at

Important rules:
- never store plain text passwords;
- never return password_hash to the frontend;
- user email should be unique.

### Todo
Represents one to-do item.

Expected fields:
- id
- user_id
- title
- description
- is_completed
- created_at
- updated_at

Relationship:
- one user can have many todos;
- one todo belongs to one user.

## API Overview
The first version may include:
```text
GET /health

POST /users/register

GET /todos
POST /todos
GET /todos/{todo_id}
PATCH /todos/{todo_id}
DELETE /todos/{todo_id}
```

Authentication can be added later as a separate feature.

For the first version, simple registration and basic todo ownership are enough.

## Validation Principles
Frontend validation improves user experience.

Backend validation protects the system.

The backend must not fully trust frontend data because API requests can be sent directly with tools like:
- FastAPI docs;
- curl;
- Postman;
- browser dev tools;
- scripts.

Use validation in several layers:
- frontend validation for user-friendly feedback;
- backend schema validation for request shape and types;
- backend service validation for business rules;
- database constraints for stored data integrity.

## Error Handling Principles

Errors should be clear and useful.

For every endpoint, consider:
- invalid request data;
- missing entity;
- duplicate data;
- database errors;
- unexpected errors.

The frontend should be able to display backend errors in a user-friendly way.

## Security Principles

Do not expose secrets.

Do not commit .env files with real values.

Use .env.example when environment variables need to be documented.

Never return private fields in API responses.

Never return password hashes.

Never store plain text passwords.

## Git Workflow

Before large changes, suggest a checkpoint:
```text
git status
git add .
git commit -m "Describe change"
After changes, suggest checking:
git diff
```

When reviewing changes, explain whether the change affects:
- frontend;
- backend;
- database;
- API contract;
- environment setup.

## Documentation Rules
If documentation is needed, create clear Markdown files.

For architecture notes, use this structure:
```text
# Architecture note title

## What problem does this solve?

## Core idea

## Frontend responsibility

## Backend responsibility

## Database responsibility

## How data flows

## Common mistakes

## Open questions

## Next step
```

For feature notes, use this structure:
```text
# Feature name

## User goal

## Frontend flow

## API request

## Backend flow

## Database changes

## Response shape

## Loading state

## Empty state

## Error state

## Testing checklist
```

## Final Instruction

Keep the application small and clear.

Build one working vertical slice first:
Register user
→ Create todo
→ Save todo in database
→ Get todos
→ Show todos in frontend

Prefer clear structure over clever abstractions.