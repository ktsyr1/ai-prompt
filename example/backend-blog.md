### **AI Prompt for Simple Blog Application Backend**

---

## **Overview**
The **Simple Blog** application allows users to create and manage their blog posts efficiently. It includes essential features like user authentication, post creation, and a simple commenting system. The application ensures security, scalability, and performance while being easy to maintain and extend.

---

## **Frameworks & Libraries**
- **Node.js**: Server-side JavaScript runtime.
- **Express.js**: Framework for building APIs.
- **MongoDB**: NoSQL database for storing user data, blog posts, and comments.
- **TypeScript**: Type-safe JavaScript for maintainability and scalability.
- **JWT (JSON Web Token)**: For secure user authentication.
- **bcryptjs**: For password hashing.
- **Mongoose**: ODM for MongoDB.
- **Jest**: Testing framework for unit and integration tests.
- **Redis**: Optional caching for performance improvement.

---

## **Project Structure**
```
├── src/
│   ├── config/             # Configuration files (MongoDB, Redis, etc.)
│   ├── controllers/        # Business logic for API routes
│   ├── middlewares/        # Authentication and validation middleware
│   ├── models/             # MongoDB schemas for users, posts, and comments
│   ├── routes/             # API routes for users, posts, and comments
│   ├── utils/              # Utility functions (e.g., error handling)
│   └── app.ts              # Application entry point
├── tests/                  # Unit and integration tests
├── .env                    # Environment variables
├── tsconfig.json           # TypeScript configuration
└── package.json            # Project dependencies and scripts
```

---

## **Database Schemas**

### **User Schema**
```ts
{
  fullName: String, // Full name of the user
  email: String,    // Unique email address for each user
  password: String, // Hashed password
  createdAt: Date,  // Date the account was created
  role: String      // User role (admin, editor, or regular user)
}
```

### **Post Schema**
```ts
{
  title: String,        // Title of the blog post
  content: String,      // Main content of the post
  author: ObjectId,     // Reference to the User who created the post
  createdAt: Date,      // Date the post was created
  updatedAt: Date,      // Date the post was last updated
  tags: [String],       // Tags for categorization
  comments: [ObjectId]  // References to comments related to the post
}
```

### **Comment Schema**
```ts
{
  postId: ObjectId,     // Reference to the associated blog post
  userId: ObjectId,     // Reference to the user who made the comment
  content: String,      // The content of the comment
  createdAt: Date       // Date the comment was made
}
```

---

## **API Endpoints**

### **Authentication**
- **POST /api/auth/register**: Create a new user account.
- **POST /api/auth/login**: Authenticate the user and return a JWT token.

### **Blog Post Management**
- **GET /api/posts**: Retrieve all blog posts.
- **GET /api/posts/:id**: Retrieve a specific blog post by its ID.
- **POST /api/posts**: Create a new blog post (authenticated users only).
- **PUT /api/posts/:id**: Update an existing blog post (authenticated users).
- **DELETE /api/posts/:id**: Delete a blog post (admins only).

### **Comment Management**
- **POST /api/posts/:id/comments**: Add a comment to a blog post.
- **GET /api/posts/:id/comments**: Retrieve all comments for a specific post.

---

## **Environment Variables**
The following environment variables should be set in the `.env` file:
- `PORT`: The port on which the server runs.
- `MONGODB_URI`: The MongoDB connection string.
- `JWT_SECRET`: Secret key for signing JWT tokens.
- `NODE_ENV`: Environment type (development, production, etc.).
- `REDIS_URL`: Redis connection string (optional for caching).

---

## **Prompts for AI**
- Use **TypeScript** for strict typing and better maintainability.
- Write **unit tests** and **integration tests** to cover all routes and functionality.
- Ensure that all **input validations** are done using **Express middlewares**.
- Implement **JWT authentication** securely with the proper handling of tokens (access & refresh tokens).
- Follow **RESTful API design** principles.
- Structure the code in a way that makes it **modular** and easy to maintain or extend in the future.
- **Password Hashing**: Use bcryptjs to securely hash user passwords before storing them.
- **JWT Authentication**: Implement secure token-based authentication for all protected routes.
- **Rate Limiting**: Use rate-limiting middleware to prevent API abuse and DoS attacks.
- **Input Validation**: Validate all incoming requests to avoid injection attacks.
- **Unit Tests**: Test individual functions like authentication, data validation, and business logic.
- **Integration Tests**: Ensure that the API endpoints work together properly by testing routes, controllers, and database interactions.
- Write code that is **easy to understand** and **well-commented**.
- Focus on **simplicity** and **scalability**.
- Always handle **error cases** (e.g., invalid inputs, unauthorized access).
- Use **modern JavaScript/TypeScript** features (e.g., destructuring, async/await).

build code this project
