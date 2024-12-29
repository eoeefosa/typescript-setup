 **complete guide** for setting up a TypeScript project with Express:

---

### 1. **Initialize the Project**
1. Create a new directory for your project and navigate to it:
   ```bash
   mkdir express-typescript-project
   cd express-typescript-project
   ```

2. Initialize a new Node.js project:
   ```bash
   npm init -y
   ```

---

### 2. **Install Required Packages**
1. Install Express and related dependencies:
   ```bash
   npm install express body-parser cors
   ```

2. Install TypeScript and its type definitions:
   ```bash
   npm install --save-dev typescript @types/node @types/express
   ```

3. Optional utilities:
   ```bash
   npm install dotenv
   npm install --save-dev ts-node nodemon
   ```

---

### 3. **Initialize TypeScript**
1. Initialize a TypeScript configuration file:
   ```bash
   npx tsc --init
   ```

2. Update `tsconfig.json` for a Node.js + Express setup:
   ```json
   {
     "compilerOptions": {
       "target": "ES2020",
       "module": "CommonJS",
       "outDir": "./dist",
       "rootDir": "./src",
       "strict": true,
       "esModuleInterop": true,
       "skipLibCheck": true
     },
     "include": ["src/**/*.ts"],
     "exclude": ["node_modules"]
   }
   ```

---

### 4. **Project Structure**
Create the following folders and files:

```
.
├── src
│   ├── app.ts
│   ├── routes
│   │   └── index.ts
│   ├── controllers
│   │   └── home.controller.ts
│   ├── middlewares
│   │   └── logger.middleware.ts
│   └── config
│       └── index.ts
├── tsconfig.json
├── package.json
├── .env
├── .gitignore
└── nodemon.json
```

---

### 5. **Write Core Files**

#### 1. **Environment Variables (`.env`)**
```env
PORT=3000
```

#### 2. **Express App Setup (`src/app.ts`)**
```ts
import express, { Application } from 'express';
import bodyParser from 'body-parser';
import cors from 'cors';
import { config } from 'dotenv';
import routes from './routes';

config(); // Load environment variables

const app: Application = express();

// Middlewares
app.use(bodyParser.json());
app.use(cors());

// Routes
app.use('/api', routes);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server is running on port ${PORT}`));
```

#### 3. **Routes (`src/routes/index.ts`)**
```ts
import { Router } from 'express';
import { getHome } from '../controllers/home.controller';

const router = Router();

router.get('/', getHome);

export default router;
```

#### 4. **Controllers (`src/controllers/home.controller.ts`)**
```ts
import { Request, Response } from 'express';

export const getHome = (req: Request, res: Response) => {
  res.json({ message: 'Welcome to Express with TypeScript!' });
};
```

#### 5. **Middleware Example (`src/middlewares/logger.middleware.ts`)**
```ts
import { Request, Response, NextFunction } from 'express';

export const logger = (req: Request, res: Response, next: NextFunction) => {
  console.log(`${req.method} ${req.url}`);
  next();
};
```

#### 6. **Config Example (`src/config/index.ts`)**
```ts
import { config } from 'dotenv';

config();

export const PORT = process.env.PORT || 3000;
```

---

### 6. **Add Scripts to `package.json`**
```json
"scripts": {
  "start": "node dist/app.js",
  "dev": "nodemon src/app.ts",
  "build": "tsc",
  "test": "echo \"No tests specified\" && exit 1"
}
```

---

### 7. **Configure Nodemon (`nodemon.json`)**
```json
{
  "watch": ["src"],
  "ext": "ts",
  "ignore": ["dist"],
  "exec": "ts-node ./src/app.ts"
}
```

---

### 8. **Run the Application**
1. Start the development server:
   ```bash
   npm run dev
   ```

2. Open your browser or Postman and navigate to:
   ```
   http://localhost:3000/api
   ```

You should see:
```json
{ "message": "Welcome to Express with TypeScript!" }
```

---

### 9. **Build for Production**
To compile the TypeScript code to JavaScript and run it:
```bash
npm run build
npm start
```

---

### 10. **Optional Enhancements**
1. **Error Handling Middleware**:
   ```ts
   app.use((err, req, res, next) => {
     console.error(err.stack);
     res.status(500).send({ error: 'Something broke!' });
   });
   ```

2. **TypeScript Linting**:
   ```bash
   npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
   ```

3. **API Documentation**:
   Use `swagger-jsdoc` and `swagger-ui-express`.

4. **Testing**:
   Use `jest` and `supertest` for unit and integration tests.

---

This guide provides a full setup for an Express project with TypeScript, including environment configuration, routing, middleware, and scripts for development and production.
