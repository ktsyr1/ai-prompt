Teklik Framework Backend Implementation - AI Prompt Style

This document outlines the backend implementation for the Teklik framework, mirroring the structure and guidelines of the provided AI prompt. It addresses security, scalability, performance, and maintainability considerations.

1. Overview

The Teklik application enables users to create, manage, and organize tasks within projects and categories. It provides features for prioritization, delegation, and task tracking.

2. Frameworks & Libraries

- Node.js: Server-side JavaScript runtime.
- Express.js: Framework for API development.
- MongoDB: Database for managing task and project data.
- TypeScript: Adds type safety and scalability.
- JSON Web Token (JWT): For user authentication.
- Refresh Tokens: For secure session management.
- Redis (v6.x): For caching and rate limiting.
- Jest (v27.x): Testing framework.
- bcryptjs: For password hashing.
- cors: For managing Cross-Origin Resource Sharing.
- jsonwebtoken: For JWT handling.
- dotenv: For loading environment variables.
- validator: For input validation.

3. Project Structure
```
├── src/
│   ├── config/
│   │   ├── database.ts        // MongoDB config
│   │   ├── redis.config.ts    // Redis config
│   │   ├── jwt.config.ts      // JWT config
│   │   ├── env.ts            // Environment variables
│   ├── controllers/
│   │   ├── tasks.ts          // Task-related controllers
│   │   ├── projects.ts        // Project-related controllers
│   │   ├── users.ts          // User-related controllers
│   ├── middlewares/
│   │   ├── auth.ts           // JWT auth middleware
│   │   ├── rateLimiter.ts    // Rate limiting middleware
│   │   ├── validator.ts      // Input validation middleware
│   │   ├── errorHandler.ts   // Error handling middleware
│   ├── models/
│   │   ├── Task.ts           // Task MongoDB model
│   │   ├── Project.ts         // Project MongoDB model
│   │   ├── User.ts           // User MongoDB model
│   ├── routes/
│   │   ├── index.ts          // Route definitions
│   ├── services/
│   │   ├── taskService.ts    // Business logic for tasks
│   │   ├── projectService.ts  // Business logic for projects
│   │   ├── userService.ts    // Business logic for users
│   ├── utils/
│   │   ├── helper.ts         // Common utility functions
│   │   └── logger.ts         // Logging utility
│   └── app.ts                // Application entry point
├── tests/
│   ├── unit/
│   │   ├── tasks.spec.ts      // Task unit tests
│   └── integration/
│       ├── tasks.integration.spec.ts //Integration tests
├── .env
├── .eslintrc.json
├── jest.config.js
├── tsconfig.json
└── package.json
``` 

4. Database Schemas (MongoDB)

```
User: fullName, email, username, password, refreshToken, lastLogin, createdAt

Task: title, description, status, createdAt, updatedAt, dueDate, projectId, assignedTo, priority, note

Project: title, description, deadline, type, budget, isLive, comments, createdAt, updatedAt

```
5. API Endpoints (Examples)

```
User: /register, /login, /refresh-token, /logout

Task: /tasks, /tasks/:id, /tasks/create, /tasks/update, /tasks/delete, /tasks/priority-update

Project: /projects, /projects/:id

```
6. Key Considerations (Security, Scalability, Performance)

Security: Implements bcrypt for password hashing, JWT for authentication, refresh tokens for long-term sessions, rate limiting, input validation, and secure storage for sensitive data.

Scalability: Leverages MongoDB for horizontal scalability and Redis for caching frequently accessed data. Design API endpoints to minimize database load.

Performance: Optimized query structures in MongoDB, lazy loading, appropriate caching strategies, and efficient handling of asynchronous operations. Implement rate limiting with Redis.

Error Handling: Comprehensive error handling, logging, and appropriate HTTP status codes.

7. Testing

Unit tests for all service functions, controllers, and middlewares.

Integration tests for interaction between different components (e.g., database interaction).

End-to-end tests for complete user flows.
