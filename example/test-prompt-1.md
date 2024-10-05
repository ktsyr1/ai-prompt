### تنفيذ المشروع

#### 1. **إعداد بيئة التطوير**

قبل البدء في بناء المشروع، تأكد من توفر الأدوات والبيئة اللازمة:

- **Node.js**: تأكد من تثبيت Node.js LTS على جهازك.
- **MongoDB**: قم بتثبيت MongoDB أو استخدام خدمة MongoDB Atlas للتخزين السحابي.
- **Redis**: قم بتثبيت Redis أو استخدام خدمة Redis Labs للتخزين المؤقت.
- **Git**: تأكد من تثبيت Git لإدارة الإصدارات.

#### 2. **إنشاء المشروع وتهيئة البيئة**

1. **إنشاء مجلد المشروع**:
   ```bash
   mkdir todo-list-backend
   cd todo-list-backend
   ```

2. **تهيئة مشروع Node.js**:
   ```bash
   npm init -y
   ```

3. **تثبيت الاعتماديات الأساسية**:
   ```bash
   npm install express mongoose redis dotenv jsonwebtoken bcryptjs joi
   npm install --save-dev typescript ts-node @types/node @types/express @types/jsonwebtoken @types/bcryptjs jest ts-jest supertest
   ```

4. **إعداد TypeScript**:
   ```bash
   npx tsc --init
   ```

5. **إعداد ESLint و Prettier**:
   ```bash
   npm install --save-dev eslint prettier eslint-config-prettier eslint-plugin-prettier
   npx eslint --init
   ```

6. **إنشاء ملف `.env`**:
   ```bash
   touch .env
   ```
   أضف المتغيرات البيئية اللازمة:
   ```env
   PORT=3000
   MONGODB_URI=mongodb://localhost:27017/todo-list
   REDIS_URL=redis://localhost:6379
   JWT_SECRET=your_jwt_secret
   REFRESH_TOKEN_SECRET=your_refresh_token_secret
   NODE_ENV=development
   ```

#### 3. **بنية المشروع**

قم بإنشاء المجلدات والملفات التالية:

```bash
mkdir src src/config src/controllers src/middlewares src/models src/routes src/utils tests
touch src/app.ts src/config/database.ts src/config/redis.ts src/middlewares/auth.ts src/middlewares/rateLimiter.ts src/models/User.ts src/models/Todo.ts src/routes/auth.ts src/routes/todos.ts src/utils/validators.ts src/utils/helpers.ts tests/auth.test.ts tests/todos.test.ts
```

#### 4. **كتابة الكود**

##### `src/app.ts`

```typescript
import express from 'express';
import mongoose from 'mongoose';
import { config } from 'dotenv';
import authRoutes from './routes/auth';
import todoRoutes from './routes/todos';
import { connectToDatabase } from './config/database';
import { connectToRedis } from './config/redis';
import { authenticate } from './middlewares/auth';
import { rateLimiter } from './middlewares/rateLimiter';

config();

const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());
app.use(rateLimiter);

app.use('/api/v1/auth', authRoutes);
app.use('/api/v1/todos', authenticate, todoRoutes);

connectToDatabase();
connectToRedis();

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

##### `src/config/database.ts`

```typescript
import mongoose from 'mongoose';

export const connectToDatabase = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URI as string);
    console.log('Connected to MongoDB');
  } catch (error) {
    console.error('Failed to connect to MongoDB', error);
  }
};
```

##### `src/config/redis.ts`

```typescript
import { createClient } from 'redis';

export const connectToRedis = async () => {
  const client = createClient({ url: process.env.REDIS_URL });
  client.on('error', (err) => console.error('Redis Client Error', err));
  await client.connect();
  console.log('Connected to Redis');
};
```

##### `src/middlewares/auth.ts`

```typescript
import { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';

export const authenticate = (req: Request, res: Response, next: NextFunction) => {
  const authHeader = req.headers.authorization;
  if (!authHeader) return res.status(401).send('Access denied. No token provided.');

  const token = authHeader.split(' ')[1];
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET as string);
    req.user = decoded;
    next();
  } catch (ex) {
    res.status(400).send('Invalid token.');
  }
};
```

##### `src/middlewares/rateLimiter.ts`

```typescript
import rateLimit from 'express-rate-limit';

export const rateLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP, please try again after 15 minutes',
});
```

##### `src/models/User.ts`

```typescript
import mongoose, { Schema, Document } from 'mongoose';
import bcrypt from 'bcryptjs';

export interface IUser extends Document {
  fullName: string;
  email: string;
  username: string;
  password: string;
  createdAt: Date;
  lastLogin: Date;
  refreshToken: string;
}

const UserSchema: Schema = new Schema({
  fullName: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  username: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  createdAt: { type: Date, default: Date.now },
  lastLogin: { type: Date },
  refreshToken: { type: String },
});

UserSchema.pre<IUser>('save', async function (next) {
  if (!this.isModified('password')) return next();
  this.password = await bcrypt.hash(this.password, 10);
  next();
});

export default mongoose.model<IUser>('User', UserSchema);
```

##### `src/models/Todo.ts`

```typescript
import mongoose, { Schema, Document } from 'mongoose';

export interface ITodo extends Document {
  title: string;
  description: string;
  status: boolean;
  createdAt: Date;
  updatedAt: Date;
  userId: mongoose.Types.ObjectId;
  dueDate?: Date;
}

const TodoSchema: Schema = new Schema({
  title: { type: String, required: true },
  description: { type: String, required: true },
  status: { type: Boolean, default: false },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now },
  userId: { type: Schema.Types.ObjectId, ref: 'User', required: true },
  dueDate: { type: Date },
});

export default mongoose.model<ITodo>('Todo', TodoSchema);
```

##### `src/routes/auth.ts`

```typescript
import express from 'express';
import { register, login, refreshToken, logout } from '../controllers/auth';

const router = express.Router();

router.post('/register', register);
router.post('/login', login);
router.post('/refresh-token', refreshToken);
router.post('/logout', logout);

export default router;
```

##### `src/routes/todos.ts`

```typescript
import express from 'express';
import { getTodos, createTodo, updateTodo, deleteTodo, getTodoById } from '../controllers/todos';

const router = express.Router();

router.get('/', getTodos);
router.post('/', createTodo);
router.put('/:id', updateTodo);
router.delete('/:id', deleteTodo);
router.get('/:id', getTodoById);

export default router;
```

##### `src/controllers/auth.ts`

```typescript
import { Request, Response } from 'express';
import bcrypt from 'bcryptjs';
import jwt from 'jsonwebtoken';
import User from '../models/User';

export const register = async (req: Request, res: Response) => {
  const { fullName, email, username, password } = req.body;
  const user = new User({ fullName, email, username, password });
  await user.save();
  res.status(201).send('User registered successfully');
};

export const login = async (req: Request, res: Response) => {
  const { username, password } = req.body;
  const user = await User.findOne({ username });
  if (!user || !await bcrypt.compare(password, user.password)) {
    return res.status(400).send('Invalid username or password');
  }

  const accessToken = jwt.sign({ _id: user._id }, process.env.JWT_SECRET as string, { expiresIn: '1h' });
  const refreshToken = jwt.sign({ _id: user._id }, process.env.REFRESH_TOKEN_SECRET as string, { expiresIn: '7d' });

  user.refreshToken = refreshToken;
  await user.save();

  res.send({ accessToken, refreshToken });
};

export const refreshToken = async (req: Request, res: Response) => {
  const { refreshToken } = req.body;
  if (!refreshToken) return res.status(401).send('No refresh token provided');

  try {
    const decoded = jwt.verify(refreshToken, process.env.REFRESH_TOKEN_SECRET as string);
    const user = await User.findById((decoded as any)._id);
    if (!user || user.refreshToken !== refreshToken) {
      return res.status(403).send('Invalid refresh token');
    }

    const accessToken = jwt.sign({ _id: user._id }, process.env.JWT_SECRET as string, { expiresIn: '1h' });
    res.send({ accessToken });
  } catch (ex) {
    res.status(400).send('Invalid refresh token');
  }
};

export const logout = async (req: Request, res: Response) => {
  const { refreshToken } = req.body;
  if (!refreshToken) return res.status(400).send('No refresh token provided');

  try {
    const decoded = jwt.verify(refreshToken, process.env.REFRESH_TOKEN_SECRET as string);
    const user = await User.findById((decoded as any)._id);
    if (!user) return res.status(404).send('User not found');

    user.refreshToken = '';
    await user.save();
    res.send('Logged out successfully');
  } catch (ex) {
    res.status(400).send('Invalid refresh token');
  }
};
```

##### `src/controllers/todos.ts`

```typescript
import { Request, Response } from 'express';
import Todo from '../models/Todo';

export const getTodos = async (req: Request, res: Response) => {
  const todos = await Todo.find({ userId: req.user._id });
  res.send(todos);
};

export const createTodo = async (req: Request, res: Response) => {
  const { title, description, dueDate } = req.body;
  const todo = new Todo({ title, description, userId: req.user._id, dueDate });
  await todo.save();
  res.status(201).send(todo);
};

export const updateTodo = async (req: Request, res: Response) => {
  const { id } = req.params;
  const { title, description, status, dueDate } = req.body;
  const todo = await Todo.findOneAndUpdate({ _id: id, userId: req.user._id }, { title, description, status, dueDate }, { new: true });
  if (!todo) return res.status(404).send('Todo not found');
  res.send(todo);
};

export const deleteTodo = async (req: Request, res: Response) => {
  const { id } = req.params;
  const todo = await Todo.findOneAndDelete({ _id: id, userId: req.user._id });
  if (!todo) return res.status(404).send('Todo not found');
  res.send('Todo deleted successfully');
};

export const getTodoById = async (req: Request, res: Response) => {
  const { id } = req.params;
  const todo = await Todo.findOne({ _id: id, userId: req.user._id });
  if (!todo) return res.status(404).send('Todo not found');
  res.send(todo);
};
```

#### 5. **اختبار التطبيق**

قم بتنفيذ الاختبارات التالية:

##### `tests/auth.test.ts`

```typescript
import request from 'supertest';
import app from '../src/app';

describe('Auth API', () => {
  it('should register a new user', async () => {
    const res = await request(app)
      .post('/api/v1/auth/register')
      .send({
        fullName: 'Test User',
        email: 'test@example.com',
        username: 'testuser',
        password: 'testpassword',
      });
    expect(res.status).toBe(201);
    expect(res.text).toBe('User registered successfully');
  });

  it('should login a user', async () => {
    const res = await request(app)
      .post('/api/v1/auth/login')
      .send({
        username: 'testuser',
        password: 'testpassword',
      });
    expect(res.status).toBe(200);
    expect(res.body).toHaveProperty('accessToken');
    expect(res.body).toHaveProperty('refreshToken');
  });
});
```

##### `tests/todos.test.ts`

```typescript
import request from 'supertest';
import app from '../src/app';

describe('Todos API', () => {
  let accessToken: string;

  beforeAll(async () => {
    const res = await request(app)
      .post('/api/v1/auth/login')
      .send({
        username: 'testuser',
        password: 'testpassword',
      });
    accessToken = res.body.accessToken;
  });

  it('should create a new todo', async () => {
    const res = await request(app)
      .post('/api/v1/todos')
      .set('Authorization', `Bearer ${accessToken}`)
      .send({
        title: 'Test Todo',
        description: 'This is a test todo',
      });
    expect(res.status).toBe(201);
    expect(res.body).toHaveProperty('_id');
  });

  it('should get all todos', async () => {
    const res = await request(app)
      .get('/api/v1/todos')
      .set('Authorization', `Bearer ${accessToken}`);
    expect(res.status).toBe(200);
    expect(res.body.length).toBeGreaterThan(0);
  });
});
```

#### 6. **تشغيل التطبيق**

قم بتشغيل التطبيق باستخدام الأمر التالي:

```bash
npm start
```

#### 7. **تشغيل الاختبارات**

قم بتشغيل الاختبارات باستخدام الأمر التالي:

```bash
npm test
```

#### 8. **نشر التطبيق**

بعد التأكد من أن التطبيق يعمل بشكل صحيح ويمر بجميع الاختبارات، يمكنك نشر التطبيق على خدمة سحابية مثل Heroku أو AWS أو أي خدمة أخرى تفضلها.

### الخاتمة

تم بناء تطبيق لإدارة المهام (Todo-list) باستخدام Node.js و Express.js و MongoDB و Redis. تم تطبيق أفضل الممارسات في الأمان، الأداء، والتوسع. يمكنك الآن توسيع التطبيق بإضافة مزيد من الميزات والتحسينات حسب الحاجة.
