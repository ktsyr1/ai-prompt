<!-- ai-prompt.md -->

# AI Prompt for blog sample  Backend

## Overview
مدونة بسيطة معها لوحة تحكم لادارتها


## Frameworks & Libraries
Node.js ،Express.js ،MongoDB ،TypeScript ،JWT ،
## Project Structure
```

├── src/
│   ├── config/            # Configuration files
│   │   ├── database.ts    # MongoDB configuration
│   ├── controllers/       # Business logic for API endpoints
│   ├── middlewares/       # Authentication
│   │   ├── auth.ts        # JWT authentication middleware
│   ├── models/            # MongoDB models for data schemas
│   ├── routes/            # API route declarations
│   └── app.ts             # Application entry point
├── .env                   # Environment variables (git-ignored)
├── .eslintrc.json         # ESLint configuration
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

### blog Schema:
- **title**: (String) 
- bio**: (String) 
- **contact**: (Boolean) 
- imageCover
- **createdAt**: (Date)
- **updatedAt**: (Date) 
- **userId**: (ObjectId) 
- **dueDate**: (Date) 
قم بتحليل البنية وقم بتحسينها

## API Endpoints
### Authentication:
- **POST /api/v1/auth/register**: Create a new user account.
- **POST /api/v1/auth/login**: Log in and return a JWT token.
- **POST /api/v1/auth/refresh-token**: Renew JWT tokens using refresh tokens.
- **POST /api/v1/auth/logout**: Invalidate the refresh token and terminate the session.
اكمل الباقية ...

## Environment Variables
....

## البرومبت
- استخدم es6 & ts في كتابة الكود 
- يجب ان يكون الكود بسيط وقابل للصيانة 
- قم بكتابة اختبارات شاملة للكود 
- عند انشاء مجلدات او ملفات اجمعها في امر واحد 
- دائما استخدم destruction فيقي استيراد كل المكونات
- قم بمراعات معايير الامان دائما عند كتابة اي كود

## التنفيذ 
قم ببناء المشروع
