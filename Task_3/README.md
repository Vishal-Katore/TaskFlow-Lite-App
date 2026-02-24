# TaskFlow Lite API

A production-ready RESTful API built with **pure Node.js** (zero external dependencies) serving as the backend for TaskFlow Lite. Replaces localStorage with server-side in-memory persistence, full middleware stack, and comprehensive test coverage.

---

## 🚀 Quick Start

```bash
# Start the server
node src/server.js

# Run automated tests (server must be running)
node tests/run-tests.js
```

Server starts at: `http://localhost:3000`

---

## 📐 Architecture

```
taskflow-api/
├── src/
│   ├── server.js                    # Entry point, HTTP server, middleware pipeline
│   ├── router.js                    # Pattern-matching router with :param support
│   ├── store.js                     # In-memory data store (Map-based, DB-ready)
│   ├── response.js                  # Standardized HTTP response helpers
│   ├── controllers/
│   │   ├── tasksController.js       # Full CRUD for tasks
│   │   └── healthController.js      # Health/uptime endpoint
│   ├── middleware/
│   │   ├── cors.js                  # CORS headers + preflight handling
│   │   ├── logger.js                # Colorized request logger with timing
│   │   └── errorHandler.js          # Centralized error handling
│   └── validators/
│       └── taskValidator.js         # Input validation & sanitization
└── tests/
    ├── run-tests.js                 # Automated test suite (22 tests, 0 deps)
    └── TaskFlow-API.postman_collection.json  # Postman/Thunder Client collection
```

---

## 🔗 API Endpoints

| Method   | Endpoint              | Description                          | Status Codes       |
|----------|-----------------------|--------------------------------------|--------------------|
| `GET`    | `/api/health`         | Health check & uptime                | 200                |
| `GET`    | `/api/tasks`          | List all tasks (filter/sort/search)  | 200, 400           |
| `POST`   | `/api/tasks`          | Create a new task                    | 201, 400           |
| `GET`    | `/api/tasks/stats`    | Aggregate statistics                 | 200                |
| `GET`    | `/api/tasks/:id`      | Get a single task                    | 200, 404           |
| `PUT`    | `/api/tasks/:id`      | Full replacement update              | 200, 400, 404      |
| `PATCH`  | `/api/tasks/:id`      | Partial update                       | 200, 400, 404      |
| `DELETE` | `/api/tasks/:id`      | Delete a task                        | 204, 404           |

---

## 📦 Request & Response Schemas

### Task Object
```json
{
  "id": "task_1706123456_abc123",
  "title": "Implement dark mode",
  "description": "Add theme toggle to the UI",
  "status": "todo | in-progress | done",
  "priority": "low | medium | high",
  "tags": ["frontend", "ui"],
  "dueDate": "2026-03-15",
  "createdAt": "2026-01-01T00:00:00.000Z",
  "updatedAt": "2026-01-02T00:00:00.000Z"
}
```

### Success Response Envelope
```json
{
  "success": true,
  "data": { ... },
  "meta": { "count": 5 },
  "timestamp": "2026-02-24T00:00:00.000Z"
}
```

### Error Response Envelope
```json
{
  "success": false,
  "error": {
    "code": 400,
    "message": "Validation failed",
    "details": [
      { "field": "title", "message": "Title is required" }
    ]
  },
  "timestamp": "2026-02-24T00:00:00.000Z"
}
```

---

## 🔍 Query Parameters for GET /api/tasks

| Param    | Values                                      | Example                          |
|----------|---------------------------------------------|----------------------------------|
| `status` | `todo`, `in-progress`, `done`               | `?status=todo`                   |
| `priority`| `low`, `medium`, `high`                   | `?priority=high`                 |
| `tag`    | Any string                                  | `?tag=frontend`                  |
| `search` | Any string (searches title + description)   | `?search=auth`                   |
| `sortBy` | `createdAt`, `updatedAt`, `title`, `priority`, `dueDate` | `?sortBy=dueDate` |
| `order`  | `asc`, `desc`                               | `?order=asc`                     |

---

## 🛡️ Middleware Stack

1. **CORS** — Configurable origins, preflight `OPTIONS` support, 24h cache
2. **Request Logger** — Colorized output with timing, unique `X-Request-ID` per request
3. **Error Handler** — Distinguishes operational vs unexpected errors, structured JSON responses

---

## 🧪 Testing

### Automated Tests (22 tests)
```bash
# Terminal 1 — start server
node src/server.js

# Terminal 2 — run tests
node tests/run-tests.js
```

### Postman / Thunder Client
Import `tests/TaskFlow-API.postman_collection.json` — includes pre/post-request scripts and test assertions for every endpoint.

---

## 🔄 Swapping to a Real Database

The `store.js` file is the only thing to replace. The interface the controllers expect:

```js
store.findAll({ status, priority, tag, search, sortBy, order })
store.findById(id)
store.create(data)
store.update(id, data)
store.delete(id)
store.stats()
```

---

## ⚙️ Environment Variables

| Variable          | Default       | Description                        |
|-------------------|---------------|------------------------------------|
| `PORT`            | `3000`        | Server port                        |
| `HOST`            | `localhost`   | Bind address                       |
| `ALLOWED_ORIGINS` | `*`           | Comma-separated allowed origins    |
| `NODE_ENV`        | `development` | Environment name                   |
