<!-- ai-prompt.md -->
# AI Prompt for Todo-list Application Backend

## Overview
The Todo-list application allows users to create, update, and manage their tasks efficiently. It ensures security, scalability, and performance through advanced caching, rate-limiting, and user authentication techniques.

## Technologies and Frameworks
- **Node.js** (v14.x or later): Server-side JavaScript runtime.
- **Express.js** (v4.x): Framework for API development.
- **MongoDB** (v4.x): Database for managing task and user data.
- **TypeScript** (v4.x): Adds type safety and scalability.
- **JWT**: For user authentication using access tokens.
- **Refresh Tokens**: Ensures secure session management.
- **Redis** (v6.x): Used for caching and performance improvements.
- **Rate Limiting**: Prevents API abuse and protects against DoS attacks.
- **Jest** (v27.x): Testing framework for unit and integration testing.

## Project Structure
```

├── src/
│   ├── config/            # Configuration files
│   ├── controllers/       # Business logic for API endpoints
│   ├── middlewares/       # Authentication, rate-limiting middleware
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

## Security Measures
- **JWT Authentication**: Uses JWTs for stateless authentication. Refresh tokens ensure long sessions while maintaining security.
- **Password Hashing**: User passwords are hashed using bcrypt before storage.
- **Helmet**: Middleware for setting various HTTP headers to secure the app from well-known web vulnerabilities.
- **Rate Limiting**: Limit requests per user/IP to prevent API abuse (e.g., 100 requests per minute for general endpoints, 10 requests per minute for login attempts).
- **Input Validation**: All incoming requests are validated using Joi or a similar library to ensure data integrity.
- **CORS**: Properly configured CORS settings to control which domains can access the API.

## Performance Optimizations
- **Redis Caching**: Frequently accessed data is cached to reduce database load and improve response times.
- **Gzip Compression**: Gzip is enabled for compressing responses, reducing bandwidth usage.
- **Pagination**: Applied to endpoints like `GET /todos` to manage large data sets effectively (default: 20 items per page).
- **Lazy Loading**: Loads data in chunks to enhance performance for large sets of todos.
- **Database Indexing**: Proper indexes on frequently queried fields to improve database performance.

## Testing
- **Unit Testing**: Each function and component is tested in isolation using Jest.
- **Integration Testing**: Ensures components interact properly with each other.
- **End-to-End Testing**: Simulates real user interactions to verify system behavior.
- **Load Testing**: Utilizes tools like Apache JMeter to test system performance under high load.

## Error Handling
- **400 Bad Request**: Invalid input or parameters.
- **401 Unauthorized**: Missing or invalid JWT token.
- **403 Forbidden**: Unauthorized access to a protected resource.
- **404 Not Found**: Resource not found, such as an invalid todo ID.
- **409 Conflict**: Duplicate entry, e.g., trying to create a user with an existing email.
- **422 Unprocessable Entity**: Valid request but unable to process due to semantic errors.
- **429 Too Many Requests**: Rate limit exceeded.
- **500 Internal Server Error**: General server-side errors.

## Logging and Monitoring
- **Winston**: Used for application logging, with different log levels for development and production.
- **Morgan**: HTTP request logging middleware for Express.js.
- **Sentry**: Error tracking and performance monitoring in production.

## Deployment and CI/CD
- **Docker**: Application is containerized for consistent deployment across environments.
- **GitHub Actions**: CI/CD pipeline for automated testing and deployment.
- **Environment Variables**: Sensitive information and configuration managed through environment variables.

## Code Style and Quality
- **ESLint**: Enforces code style and catches potential errors.
- **Prettier**: Ensures consistent code formatting across the project.
- **Husky**: Pre-commit hooks to run linting and tests before allowing commits.

## API Documentation
- **Swagger/OpenAPI**: Interactive API documentation generated and served at `/api-docs`.

## Internationalization
- **i18next**: Internationalization library for translating error messages and API responses.

## Accessibility
- Structured API responses to support screen readers and other assistive technologies.
- Consistent and semantic error codes and messages.

## Suggestions for Improvement
- **Multi-Factor Authentication (MFA)**: Consider adding support for MFA to improve account security.
- **GraphQL**: Evaluate the benefits of implementing a GraphQL API alongside REST for more flexible data querying.
- **WebSocket Support**: For real-time updates on collaborative todo lists or instant notifications.
- **Microservices Architecture**: Consider breaking down the application into microservices for improved scalability and maintainability as the application grows.
- **OAuth Integration**: Add support for social login (Google, Facebook, etc.) to simplify the authentication process for users. 
