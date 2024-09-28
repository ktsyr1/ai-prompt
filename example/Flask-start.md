
### AI Prompt for Todo-list Application Backend (Flask)

## Overview
The Todo-list application allows users to create, update, and manage their tasks efficiently with Flask. The application ensures security, scalability, and performance through advanced caching, rate-limiting, and user authentication techniques.

## Frameworks & Libraries
- **Flask**
- **MongoDB**
- **Python (v3.x)**
- **Flask-JWT-Extended**
- **Redis**  
- **pytest** 
- **bcrypt** 
- **Flask-Limiter** 

## Project Structure
```
├── app/
│   ├── __init__.py         # App initialization and configuration
│   ├── config.py           # Application configuration file
│   ├── controllers/        # Business logic for API endpoints
│   │   ├── auth.py         # Authentication-related logic
│   │   ├── todo.py         # Todo-related logic
│   ├── models/             # MongoDB models for data schemas
│   │   ├── user.py         # User schema
│   │   ├── todo.py         # Todo schema
│   ├── middlewares/        # JWT authentication, rate limiting
│   │   ├── auth.py         # JWT authentication middleware
│   │   └── rate_limiter.py # Rate limiting configuration and middleware
│   ├── routes/             # API route declarations
│   │   ├── auth.py         # Routes for authentication
│   │   ├── todo.py         # Routes for todo management
│   ├── utils/              # Utility functions and helpers
│   ├── tests/              # Test files for unit and integration tests
├── .env                    # Environment variables (git-ignored)
├── requirements.txt        # Python dependencies
├── pytest.ini              # pytest configuration
└── run.py                  # Application entry point
```

## Database Schemas

### User Schema:
```python
from mongoengine import Document, StringField, DateTimeField, ReferenceField
import datetime

class User(Document):
    full_name = StringField(required=True)
    email = StringField(required=True, unique=True)
    username = StringField(required=True, unique=True)
    password = StringField(required=True)
    created_at = DateTimeField(default=datetime.datetime.utcnow)
    last_login = DateTimeField(default=None)
    refresh_token = StringField(default=None)
```

### Todo Schema:
```python
from mongoengine import Document, StringField, BooleanField, DateTimeField, ReferenceField
import datetime

class Todo(Document):
    title = StringField(required=True)
    description = StringField()
    status = BooleanField(default=False)
    created_at = DateTimeField(default=datetime.datetime.utcnow)
    updated_at = DateTimeField(default=None)
    user = ReferenceField(User, required=True)
    due_date = DateTimeField(default=None)
```

## API Endpoints

### Authentication:
- **POST /api/v1/auth/register** 
- **POST /api/v1/auth/login** 
- **POST /api/v1/auth/refresh-token** 
- **POST /api/v1/auth/logout** 

### Todo Management:
- **GET /api/v1/todos** 
- **POST /api/v1/todos** 
- **GET /api/v1/todos/:id** 
- **PUT /api/v1/todos/:id** 
- **DELETE /api/v1/todos/:id** 

## Environment Variables
The following environment variables should be set in the `.env` file:
- `PORT`: The port on which the server will run.
- `MONGODB_URI`: Connection string for MongoDB.
- `REDIS_URL`: Connection string for Redis.
- `JWT_SECRET_KEY`: Secret key for signing JWTs.
- `REFRESH_TOKEN_SECRET_KEY`: Secret key for signing refresh tokens.
- `FLASK_ENV`: Current environment (development, production, test).

## البرومبت 
- احتفظ بالبيانات الحساسة مثل مفاتيح JWT في ملف .env ولا تدرجها في الكود
- استخدم أسماء متغيرات واضحة، وأعط دوالك تسميات تعبر عن وظيفتها
- تأمين البيانات: استخدم bcrypt لتأمين كلمات المرور.
- استخدام JWT: تأكد من حماية نقاط النهاية باستخدام JWT.
- التحقق من المدخلات: تحقق من صحة المدخلات لضمان عدم وجود هجمات
- استخدم unpacking عند استيراد الدوال أو الكلاسات
- استخدم pytest لكتابة اختبارات الوحدة. وتأكد من اختبار جميع وظائف API والدوال الهامة.
- استخدم أوامر مثل mkdir -p app/models لإنشاء المجلدات بشكل جماعي
-  استخدم الميزات الحديثة في Python مثل f-strings وlist comprehensions
-  قم بكتابة دوال reusable (قابلة لإعادة الاستخدام) بدلًا من تكرار الكود
 

## التنفيذ 
قم ببناء المشروع
