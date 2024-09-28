### AI Prompt for Todo-list Application Frontend (React Native)

## Overview
The Todo-list application allows users to create, update, and manage their tasks efficiently using React Native. The application emphasizes a smooth user experience with robust state management, validation, and navigation.

## Frameworks & Libraries
- **React Native**
- **Redux** (for state management)
- **React Navigation** (for routing)
- **Axios** (for API requests)
- **React Native Paper** (for UI components)
- **React Hook Form** (for form management) 

## Project Structure
```
├── src/
│   ├── components/         # Reusable components
│   ├── screens/            # Main screens of the application
│   │   ├── LoginScreen.js   
│   │   ├── RegisterScreen.js
│   │   ├── TodoListScreen.js
│   │   ├── TodoDetailScreen.js
│   │   ├── AddTodoScreen.js
│   ├── redux/              # Redux state management
│   ├── services/           # API service calls
│   ├── utils/              # Utility functions and helpers
│   ├── App.js              # Main application entry point
│   ├── navigation/         # Navigation setup
│   ├── .env                # Environment variables (git-ignored)
├── package.json            # Project dependencies
└── App.test.js             # Test file for the main app component
```

## Screens
- **Login Screen** 
- **Register Screen** 
- **Todo List Screen**: 
- **Todo Detail Screen**: 
- **Add Todo Screen**: 
 
## Environment Variables
- `API_URL`: Base URL for the backend API.
- `JWT_SECRET_KEY`: Secret key for JWTs.
- `APP_ENV`: Current environment (development, production).

## البرومبت

- استخدم Redux لإدارة الحالة.
- تحقق من المدخلات باستخدام React Hook Form .
- تأمين التطبيق باستخدام JWT لحماية الوصول إلى الشاشات.
- استخدم Redux Thunk أو Saga للتعامل مع الطلبات غير المتزامنة.
- استخدم React Navigation للتنقل بين الشاشات بسهولة.
- قم بإنشاء مكونات قابلة لإعادة الاستخدام لتجنب تكرار الكود.

## التنفيذ 
قم ببناء المشروع
