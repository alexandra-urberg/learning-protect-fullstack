# Claude Instructions for Frontend

You are assisting with the frontend part of the to-do application.

The frontend communicates with the backend through API requests.

---

# Frontend Stack

Use:

- React
- TypeScript
- CSS
- Vite if a build setup is needed

Avoid adding large UI libraries unless explicitly requested.

Keep styling simple and clear.

---

# Frontend Goal

The frontend should allow a user to:

- register;
- create a todo;
- view todos;
- update a todo;
- mark a todo as completed or not completed;
- delete a todo.

The frontend should clearly handle:

- loading states;
- empty states;
- success states where useful;
- error states;
- backend validation errors.

---

# Frontend Responsibilities

The frontend is responsible for:

- rendering UI;
- collecting form input;
- doing user-friendly validation;
- sending API requests;
- displaying backend responses;
- showing loading states;
- showing empty states;
- showing errors;
- updating UI after successful actions.

The frontend should not be the only place where important validation exists.

Backend validation is still required.

---

# Suggested Frontend Structure

Use a simple structure first:

```text
frontend/
├── src/
│   ├── api/
│   │   ├── client.ts
│   │   ├── usersApi.ts
│   │   └── todosApi.ts
│   ├── components/
│   ├── features/
│   │   ├── registration/
│   │   └── todos/
│   ├── types/
│   ├── App.tsx
│   └── main.tsx
├── package.json
└── vite.config.ts
```

If the existing structure is different, explain before changing it.

## API Communication

Keep API logic separated from UI components.

Prefer API functions like:
```TypeScript
registerUser()
getTodos()
createTodo()
updateTodo()
deleteTodo()
```

Components should call these functions instead of putting `fetch` logic everywhere.

Example flow:
```text
User submits form
→ component calls API function
→ API function sends request
→ backend returns response
→ component updates state
→ UI updates
```


## API Endpoints

The frontend may use these endpoints:
```text
POST /users/register

GET /todos
POST /todos
GET /todos/{todo_id}
PATCH /todos/{todo_id}
DELETE /todos/{todo_id}
```

For each API request, define:
- request body;
- response type;
- loading state;
- success behavior;
- error behavior.


## TypeScript Rules

Use TypeScript types for API request and response data.

Example:
```TypeScript
export type UserResponse = {
  id: number;
  email: string;
  created_at: string;
};

export type Todo = {
  id: number;
  user_id: number;
  title: string;
  description: string | null;
  is_completed: boolean;
  created_at: string;
  updated_at: string;
};
```

Important:
TypeScript helps during development, but it does not replace backend validation.

The backend still validates real incoming data.


## Form Rules

Forms should be simple and understandable.

For each form, handle:
- input value;
- submit state;
- simple frontend validation;
- disabled submit state when needed;
- backend error messages;
- success behavior.

Frontend validation should improve user experience.

Example:
- show “Email is required” before sending registration request;
- show “Todo title is required” before sending create todo request.

But backend validation must still exist.


## Registration UI

The registration UI should include:
- email input;
- password input;
- submit button;
- loading state;
- error message;
- success state.

When registration succeeds, the frontend can:
- show success message;
- store returned user data in local state;
- move to todo list screen.

Do not store password.

Do not display password hash.


## Todo UI

The todo UI should include:
- todo list;
- create todo form;
- checkbox or button for completed state;
- edit behavior;
- delete button;
- loading state;
- empty state;
- error state.

Empty state example:
```text
No todos yet. Create your first todo.
```

Error state example:
```text
Could not load todos. Please try again.
```


## Frontend State Rules

Start with simple local React state.

Do not add state management libraries unless needed.

For the first version, local state is enough.

Track states like:
```TypeScript
const [todos, setTodos] = useState<Todo[]>([]);
const [isLoading, setIsLoading] = useState(false);
const [error, setError] = useState<string | null>(null);
```

Later, if the project grows, state management can be reconsidered.


## Error Handling Rules

The frontend should handle:
- network errors;
- backend validation errors;
- not found errors;
- duplicate email errors;
- unexpected errors.

When backend returns an error, show a useful message to the user.

Do not expose raw technical errors if they are confusing.


## Loading and Empty States

Every async request should have a loading state where useful.

Todo list should have:
- loading state while fetching;
- empty state when there are no todos;
- error state when request fails;
- normal state when todos exist.

Submit buttons should avoid duplicate submissions when a request is already in progress.

## Accessibility Rules
Use semantic HTML where possible.

Prefer:
- <form> for forms;
- <button> for actions;
- <label> connected to inputs;
- clear error messages;
keyboard-accessible controls.
Do not use clickable <div> elements for buttons.
When showing form errors, make them easy to understand.


## Styling Rules

Use simple CSS.

Prefer readable layout over complex styling.

Do not add design system complexity unless requested.

Keep components small and clear.


## Frontend-Backend Contract

The frontend should follow the backend API contract.

When backend response shape changes, update frontend types and API functions.

When frontend needs different data, first check whether the backend endpoint should change.

For every feature, consider:
- what data the UI needs;
- whether the API already returns it;
- whether the request body matches the backend schema;
- how errors are returned;
- how errors are displayed.


## Testing API Manually

During development, backend endpoints can be tested with FastAPI docs:
```text
http://localhost:8000/docs
```

The frontend should be tested by real user flows:
Open app
→ register user
→ create todo
→ see todo in list
→ update todo
→ delete todo

## Frontend Change Checklist

Before making frontend changes, consider:
- Does this need an API call?
- Does this need a loading state?
- Does this need an error state?
- Does this need an empty state?
- Does this need frontend validation?
- Does the backend also validate this?
- Does this match the backend response type?

After making frontend changes, explain:
- what changed;
- which files changed;
- how to test the UI;
- whether backend changes are also needed.


## Final Frontend Instruction

Keep the frontend simple at first.

First goal:
Registration form
→ Todo list
→ Create todo
→ Update todo
→ Delete todo

Focus on clear data flow:
UI action → API request → backend response → UI update