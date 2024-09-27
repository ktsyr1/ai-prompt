<!-- ai-prompt.md -->

# AI Prompt for Todo-list Application Backend

## Overview
The Todo-list application allows users to create, update, and manage their tasks efficiently. It ensures security, scalability, and performance through advanced caching, rate-limiting, and user authentication techniques.

## Frameworks & Libraries
- **Node.js**: Server-side JavaScript runtime.
- **Express.js**: Framework for API development.
- **MongoDB**: Database for managing task and user data.
- **TypeScript**: Adds type safety and scalability.
- **JWT**: For user authentication using access tokens.
- **Refresh Tokens**: Ensures secure session management.
- **Redis** (v6.x): Used for caching and performance improvements. 
- **Jest** (v27.x): Testing framework for unit and integration tests.
- Rate Limiting
## Project Structure
```

├── src/
│   ├── config/            # Configuration files
│   │   ├── database.ts    # MongoDB configuration
│   │   └── redis.ts       # Redis configuration
│   ├── controllers/       # Business logic for API endpoints
│   ├── middlewares/       # Authentication, rate-limiting middleware
│   │   ├── auth.ts        # JWT authentication middleware
│   │   └── rateLimiter.ts # Rate limiting configuration and middleware
│   ├── models/            # MongoDB models for data schemas
│   ├── routes/            # API route declarations
│   ├── utils/             # Utility functions and helpers
│   └── app.ts             # Application entry point
├── tests/                 # Test files for unit and integration tests
├── .env                   # Environment variables (git-ignored)
├── .eslintrc.json         # ESLint configuration
├── jest.config.js         # Configuration for Jest
├── tsconfig.json          # TypeScript configuration
└── package.json           # Project dependencies and scripts
```

## Database Schemas
### User Schema:
- **fullName**: (String) Full name of the user.
- **email**: (String) Unique email address.
- **username**: (String) Unique username for login.
- **password**: (String) Hashed password using bcrypt.
- **createdAt**: (Date) Date of account creation.
- **lastLogin**: (Date) Date of last successful login.
- **refreshToken**: (String) Refresh token for session management.

### Todo Schema:
- **title**: (String) Task title.
- **description**: (String) Description of the task.
- **status**: (Boolean) Task completion status.
- **createdAt**: (Date) Date of task creation.
- **updatedAt**: (Date) Date of last task update.
- **userId**: (ObjectId) Reference to the user.
- **dueDate**: (Date) Optional due date for the task.

## API Endpoints
### Authentication:
- **POST /api/v1/auth/register**: Create a new user account.
- **POST /api/v1/auth/login**: Log in and return a JWT token.
- **POST /api/v1/auth/refresh-token**: Renew JWT tokens using refresh tokens.
- **POST /api/v1/auth/logout**: Invalidate the refresh token and terminate the session.

### Todo Management:
- **GET /api/v1/todos**: Retrieve all todos for the authenticated user.
- **POST /api/v1/todos**: Create a new todo item.
- **PUT /api/v1/todos/:id**: Update an existing todo by its ID.
- **DELETE /api/v1/todos/:id**: Delete a todo by its ID.
- **GET /api/v1/todos/:id**: Retrieve a specific todo by its ID.

## Environment Variables
The following environment variables should be set in the `.env` file:
- `PORT`: The port on which the server will run.
- `MONGODB_URI`: Connection string for MongoDB.
- `REDIS_URL`: Connection string for Redis.
- `JWT_SECRET`: Secret key for signing JWTs.
- `REFRESH_TOKEN_SECRET`: Secret key for signing refresh tokens.
- `NODE_ENV`: Current environment (development, production, test).

## البرومبت
- استخدم es6 & ts في كتابة الكود 
- يجب ان يكون الكود بسيط وقابل للصيانة 
- قم بكتابة اختبارات شاملة للكود 
- عند انشاء مجلدات او ملفات اجمعها في امر واحد 
- دائما استخدم destruction فيقي استيراد كل المكونات
- قم بمراعات معايير الامان دائما عند كتابة اي كود

## التنفيذ 
قم ببناء المشروع
