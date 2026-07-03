<!-- Header Badges -->
<div align="center">

# 🛠️ Backend (Prisma-Express) Server সেটআপ গাইড

![Prisma](https://img.shields.io/badge/Prisma-ORM-2D3748?style=for-the-badge&logo=prisma&logoColor=white)
![npm](https://img.shields.io/badge/npm-Package_Manager-CB3837?style=for-the-badge&logo=npm&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-ESM-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Express](https://img.shields.io/badge/Express-Framework-000000?style=for-the-badge&logo=express&logoColor=white)

**Express**, **Prisma ORM**, এবং package manager হিসেবে **npm** দিয়ে একটি **TypeScript backend** সেটআপ করার ধাপে ধাপে রেফারেন্স গাইড।

📖 [Official Prisma Docs](https://www.prisma.io/docs/prisma-postgres/quickstart/prisma-orm#1-create-a-new-project)

</div>

---

## 📋 সূচিপত্র

- [🛠️ Backend Setup Guide](#️-backend-setup-guide)
  - [📋 সূচিপত্র](#-সূচিপত্র)
  - [⚡ Quick Reference](#-quick-reference)
  - [Step 1 — Project Initialization](#step-1--project-initialization)
  - [Step 2 — Dependencies ইনস্টল করা](#step-2--dependencies-ইনস্টল-করা)
  - [Step 3 — TypeScript (ESM) কনফিগার করা](#step-3--typescript-esm-কনফিগার-করা)
  - [Step 4 — Prisma Initialize করা](#step-4--prisma-initialize-করা)
  - [Step 5 — Environment Variable সেটআপ করা](#step-5--environment-variable-সেটআপ-করা)
  - [Step 6 — Prisma Schema Strategy](#step-6--prisma-schema-strategy)
  - [Step 7 — Database Migration](#step-7--database-migration)
  - [Step 8 — Prisma Client Generate করা](#step-8--prisma-client-generate-করা)
  - [Step 9 — Prisma Client Instantiate করা](#step-9--prisma-client-instantiate-করা)
  - [Step 10 — Environment Config Module](#step-10--environment-config-module)
  - [Step 11 — Express App তৈরি করা](#step-11--express-app-তৈরি-করা)
  - [Step 12 — Server Bootstrap করা](#step-12--server-bootstrap-করা)
  - [Step 13 — package.json Scripts কনফিগার করা](#step-13--packagejson-scripts-কনফিগার-করা)

---

## ⚡ Quick Reference

> যদি আপনি সেটআপ সম্পর্কে আগে থেকেই জানেন, তাহলে সরাসরি কমান্ডে চলে যান।

| #   | কাজ                     | Command                     |
| --- | ------------------------ | --------------------------- |
| 1   | Prisma Initialize করা        | `npx prisma init`           |
| 2   | Migration চালানো          | `npx prisma migrate dev`    |
| 3   | Prisma Client Generate করা   | `npx prisma generate`       |
| 4   | Prisma Studio (GUI) খোলা | `npx prisma studio`         |
| 5   | Deploy (শুধু Production-এর জন্য) | `npx prisma migrate deploy` |
| 6   | Dev Server চালু করা         | `npm run dev`               |
| 7   | Production-এর জন্য Build করা     | `npm run build`             |
| 8   | Production Server চালু করা  | `npm run start`             |

---

## Step 1 — Project Initialization

আপনার project folder তৈরি ও সেটআপ করুন:

```bash
mkdir your-project-name
cd your-project-name
```

Git initialize করুন এবং একটি `.gitignore` তৈরি করুন:

```bash
git init
```

📄 `.gitignore`

```text
node_modules
dist
.env
```

> [!IMPORTANT]
> আপনার database credentials যাতে প্রকাশ না হয়ে যায়, তার জন্য সবসময় `.env` কে `.gitignore`-এ যোগ করুন।

একটি npm project initialize করুন:

```bash
npm init -y
```

## Step 2 — Dependencies ইনস্টল করা

### 🔧 Core Tooling

📦 Dev Dependencies — TypeScript tooling + Prisma CLI:

```bash
npm install -D typescript tsx @types/node prisma
```

📦 Runtime Dependencies — Prisma Client + PostgreSQL adapter + dotenv:

```bash
npm install @prisma/client @prisma/adapter-pg dotenv
```

TypeScript config initialize করুন:

```bash
npx tsc --init
```

> [!NOTE]
> Bun-এর মতো নয়, Node.js স্বয়ংক্রিয়ভাবে `.env` file load করে না। এর জন্য `dotenv` package প্রয়োজন, এবং আপনার application-এর entry point-এ অবশ্যই `import 'dotenv/config'` (অথবা `dotenv.config()`) কল করতে হবে।

### 🌐 Express Server ও Middleware

📦 Runtime Dependencies — Express + middleware packages:

```bash
npm install express cors jsonwebtoken cookie-parser http-status bcrypt
```

📦 Dev Dependencies — TypeScript type definitions:

```bash
npm install -D @types/express @types/cors @types/jsonwebtoken @types/cookie-parser @types/bcrypt
```

### 📖 Package বর্ণনা

**Runtime Packages:**

| Package        | বর্ণনা                                                                                          |
| --------------- | ----------------------------------------------------------------------------------------------- |
| express         | API তৈরি এবং HTTP request/response হ্যান্ডেল করার জন্য একটি minimal এবং flexible Node.js web framework    |
| cors            | একটি middleware যা Cross-Origin Resource Sharing চালু করে, যার ফলে আপনার API বিভিন্ন domain/port থেকে request গ্রহণ করতে পারে |
| jsonwebtoken    | authentication এবং authorization-এর জন্য JWT (JSON Web Tokens) sign ও verify করতে ব্যবহৃত হয়                    |
| cookie-parser   | একটি middleware যা incoming request-এ যুক্ত cookie parse করে, যা `req.cookies`-এর মাধ্যমে অ্যাক্সেসযোগ্য হয় |
| http-status     | মানুষের পড়ার উপযোগী HTTP status code constant প্রদান করে (যেমন, `200`-এর বদলে `httpStatus.OK`)                  |
| bcrypt          | database-এ সংরক্ষণ করার আগে password নিরাপদভাবে hash এবং compare করতে ব্যবহৃত হয়                        |

**Dev Packages (Type Definitions):**

| Package                | বর্ণনা                                    |
| ----------------------- | ----------------------------------------------- |
| @types/express          | Express-এর জন্য TypeScript type definitions         |
| @types/cors             | cors-এর জন্য TypeScript type definitions            |
| @types/jsonwebtoken      | jsonwebtoken-এর জন্য TypeScript type definitions    |
| @types/cookie-parser     | cookie-parser-এর জন্য TypeScript type definitions   |
| @types/bcrypt            | bcrypt-এর জন্য TypeScript type definitions          |

> [!NOTE]
> `http-status`-এর নিজস্ব type definitions রয়েছে, তাই আলাদা `@types/http-status` package-এর প্রয়োজন নেই।

### 🐘 PostgreSQL Client

📦 Runtime Dependencies — Node.js-এর জন্য PostgreSQL client:

```bash
npm install pg
```

📦 Dev Dependencies — TypeScript type definitions:

```bash
npm install -D @types/pg
```

### 📖 Package বর্ণনা

| Package | ধরন    | বর্ণনা                                                                                   |
| ------- | ------- | ----------------------------------------------------------------------------------------------- |
| pg      | Runtime | Node.js-এর জন্য PostgreSQL client — যা আপনার PostgreSQL database-এর সাথে সরাসরি যোগাযোগ করতে দেয়   |
| @types/pg | Dev   | pg-এর জন্য TypeScript type definitions                                                              |

> [!NOTE]
> `pg` হলো সেই underlying driver যার উপর `@prisma/adapter-pg` PostgreSQL-এর সাথে যোগাযোগের জন্য নির্ভর করে। এটি আলাদাভাবে ইনস্টল করলে Prisma-এর বাইরেও প্রয়োজনে সরাসরি PostgreSQL client অ্যাক্সেস পাওয়া যায়।

## Step 3 — TypeScript (ESM) কনফিগার করা

ESM compatibility-এর জন্য আপনার `tsconfig.json`-এর কনটেন্ট নিচেরটি দিয়ে প্রতিস্থাপন করুন:

```json
{
  "compilerOptions": {
    "outDir": "./dist",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "target": "ES2023",
    "types": ["node"],
    "sourceMap": true,
    "declaration": true,
    "declarationMap": true,
    "noUncheckedIndexedAccess": true,
    "strict": true,
    "isolatedModules": true,
    "noUncheckedSideEffectImports": true,
    "moduleDetection": "force",
    "skipLibCheck": true
  },
  // "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

> [!TIP]
> আপনার source file গুলো `/src` directory-তে সরিয়ে নেওয়ার পর `"include": ["src/**/*"]` লাইনটি uncomment করুন।

## Step 4 — Prisma Initialize করা

একটি custom client output path সহ Prisma init কমান্ড চালান:

```bash
npx prisma init --output ../generated/prisma
```

✅ এই কমান্ড স্বয়ংক্রিয়ভাবে নিচের ফাইলগুলো তৈরি করে:

| তৈরি হওয়া আইটেম          | বর্ণনা                                  |
| ------------------------ | --------------------------------------------- |
| `prisma/schema.prisma`   | আপনার database schema definition file          |
| `.env`                   | environment variable-এর জন্য root-level file     |
| `prisma.config.ts`       | Prisma configuration file                     |

> [!IMPORTANT]
> npm ব্যবহার করার সময়, Prisma CLI কমান্ড চালাতে সবসময় `npx` ব্যবহার করুন। `npx` `node_modules/.bin/`-এ থাকা locally installed binary চালায়।

### 📄 তৈরি হওয়া File-এর কনটেন্ট

`prisma init` চালানোর পর, নিচের file গুলো এই default কনটেন্ট সহ তৈরি হয়:

**`prisma.config.ts`** (default — Step 6-এ আপডেট করা হবে)

```typescript
import "dotenv/config";
import { defineConfig, env } from "prisma/config";

export default defineConfig({
  schema: "prisma/schema.prisma",
  migrations: {
    path: "prisma/migrations",
  },
  datasource: {
    url: env("DATABASE_URL"),
  },
});
```

**`prisma/schema.prisma`** (default — কাঠামো Step 6-এ বেছে নেওয়া schema strategy-এর উপর নির্ভর করে)

```prisma
generator client {
  provider = "prisma-client"
  output   = "../generated/prisma"
}

datasource db {
  provider = "postgresql"
}
```

> [!NOTE]
> schema-টি ESM-first `prisma-client` generator ব্যবহার করে, যার output path কাস্টম করে `../generated/prisma`-তে দেওয়া হয়েছে। এর ফলে generated client, cleaner import-এর জন্য `prisma/` directory-এর বাইরে থাকে।

## Step 5 — Environment Variable সেটআপ করা

1. Prisma Console-এর মাধ্যমে একটি Prisma Postgres database তৈরি করুন
2. CLI output থেকে আপনার connection string কপি করুন
3. আপনার `.env` file-এ placeholder value-টি প্রতিস্থাপন করুন:

```env
DATABASE_URL="postgres://your-connection-string-here"
```

> [!CAUTION]
> কখনও আপনার `.env` file Git-এ commit করবেন না। এতে sensitive credentials থাকে।

## Step 6 — Prisma Schema Strategy

model লেখার আগে, আপনি কীভাবে আপনার Prisma schema সংগঠিত করতে চান তা ঠিক করুন। Prisma single-file এবং multi-file — দুই ধরনের approach-ই সাপোর্ট করে।

### 📁 Option A — Single File Schema (ছোট project-এর জন্য)

প্রায় ৫–১০টি model-সহ ছোট project-এর জন্য, সবকিছু একটি file-এ রাখাই সবচেয়ে সহজ এবং maintainable বিকল্প।

Directory কাঠামো:

```text
prisma/
├── migrations/
└── schema.prisma
```

`prisma.config.ts`-এ কোনো পরিবর্তনের প্রয়োজন নেই — default generated config ইতিমধ্যেই `prisma/schema.prisma`-কে নির্দেশ করে এবং সরাসরি কাজ করে।

### 📂 Option B — Multi-File Schema (মাঝারি থেকে বড় project-এর জন্য সুপারিশকৃত)

যদি আপনার project-এ ১৫+ model থাকে, একাধিক developer থাকে, অথবা স্পষ্টভাবে আলাদা করা domain থাকে (users, products, orders, payments ইত্যাদি), তাহলে schema-কে একাধিক file-এ ভাগ করলে organization এবং maintainability উন্নত হয়।

Directory কাঠামো:

```text
prisma/
├── migrations/
├── schema/
│   ├── schema.prisma       ← generator + datasource block এখানে থাকে
│   ├── user.prisma
│   ├── product.prisma
│   ├── order.prisma
│   ├── payment.prisma
│   └── review.prisma
```

> [!NOTE]
> মাঝারি এবং বড় project-এর জন্য multi-file Prisma schema ব্যবহার করাকে professional ও scalable approach হিসেবে গণ্য করা হয়। এটি সেরা পছন্দ কিনা, তা নির্ভর করে আপনার application-এর আকার এবং জটিলতার উপর।

### ✏️ Multi-File Schema-এর জন্য prisma.config.ts আপডেট করা

multi-file approach ব্যবহার করার সময়, `prisma.config.ts`-এর `schema` property-কে একটি single file-এর বদলে directory-কে নির্দেশ করার জন্য আপডেট করুন। এছাড়াও optional seed script path যোগ করুন:

📄 `prisma.config.ts`

```typescript
import { defineConfig, env } from "prisma/config";
import "dotenv/config";

export default defineConfig({
  schema: "prisma/schema",
  migrations: {
    path: "prisma/migrations",
    seed: "tsx prisma/seed.ts",
  },
  datasource: {
    url: env("DATABASE_URL"),
  },
});
```

### 🔍 Default Config থেকে যা পরিবর্তন হয়েছে

| Property         | Default Value             | আপডেট করা Value          | কারণ                                                              |
| ----------------- | -------------------------- | ------------------------ | --------------------------------------------------------------------- |
| `schema`          | `"prisma/schema.prisma"`   | `"prisma/schema"`       | একটি single file-এর বদলে directory-কে নির্দেশ করে                       |
| `migrations.seed` | (সেট করা নেই)                   | `"tsx prisma/seed.ts"`   | database-এ প্রাথমিক data populate করার জন্য একটি seed script রেজিস্টার করে   |

> [!TIP]
> `generator client` block এবং `datasource db` block অবশ্যই schema directory-এর ভেতরে থাকা কোনো `.prisma` file-এ থাকতে হবে — সাধারণত `schema/schema.prisma` অথবা একটি আলাদা `schema/base.prisma`-তে।

> [!WARNING]
> single-file এবং multi-file approach একসাথে মেশাবেন না। যদি `schema` কোনো directory-কে নির্দেশ করে, তাহলে Prisma আশা করে সেই directory-এর ভেতরের সব `.prisma` file মিলে সম্পূর্ণ schema তৈরি করবে — `prisma/` root level-এ কোনো `schema.prisma` থাকা যাবে না।

এখন আপনি আপনার schema model লেখার জন্য প্রস্তুত। ✅

## Step 7 — Database Migration

📖 [Prisma Migrate Docs](https://www.prisma.io/docs/orm/prisma-migrate)

আপনার schema file-এ model define করার পর, একটি migration তৈরি ও apply করুন:

```bash
npx prisma migrate dev
```

migration-এর একটি নাম দিতে বলা হবে। উদাহরণ:

```bash
✔ Enter a name for the new migration: › init
```

> [!WARNING]
> `migrate dev` শুধুমাত্র development-এর জন্য ব্যবহৃত একটি কমান্ড। production environment-এর জন্য ব্যবহার করুন:

```bash
npx prisma migrate deploy
```

## Step 8 — Prisma Client Generate করা

migrate করার পর, Prisma Client ম্যানুয়ালি generate করুন:

```bash
npx prisma generate
```

> [!IMPORTANT]
> Prisma v7+ Breaking Change: `migrate dev` আর স্বয়ংক্রিয়ভাবে `prisma generate` ট্রিগার করে না। প্রতিটি migration বা schema পরিবর্তনের পর আপনাকে অবশ্যই এই ধাপটি স্পষ্টভাবে চালাতে হবে।

> [!TIP]
> আপনার schema file পরিবর্তন করলেই আপনার Prisma Client sync-এ রাখতে `npx prisma generate` পুনরায় চালান।

## Step 9 — Prisma Client Instantiate করা

Prisma Client instantiate ও export করার জন্য একটি dedicated file তৈরি করুন। এতে আপনার database connection পুরো project জুড়ে কেন্দ্রীভূত এবং reusable থাকে।

📄 `src/lib/prisma.ts`

```typescript
import "dotenv/config";
import { PrismaPg } from "@prisma/adapter-pg";
import { PrismaClient } from "../generated/prisma/client";

const connectionString = `${process.env.DATABASE_URL}`;
const adapter = new PrismaPg({ connectionString });
const prisma = new PrismaClient({ adapter });

export { prisma };
```

### 🔍 প্রতিটি লাইন কী করে

| লাইন                                 | কাজ                                                                |
| -------------------------------------- | ------------------------------------------------------------------------ |
| `import "dotenv/config"`               | `.env` থেকে environment variable গুলো `process.env`-এ load করে             |
| `PrismaPg`                             | Prisma এবং `pg`-এর মধ্যে সংযোগকারী PostgreSQL driver adapter             |
| `PrismaClient`                         | আপনার schema থেকে auto-generated Prisma Client                       |
| `new PrismaPg({ connectionString })`   | আপনার `DATABASE_URL` ব্যবহার করে একটি `pg`-ভিত্তিক adapter তৈরি করে                 |
| `new PrismaClient({ adapter })`        | PostgreSQL adapter সহ Prisma Client instantiate করে         |
| `export { prisma }`                    | আপনার পুরো application জুড়ে ব্যবহারের জন্য client instance export করে        |

> [!TIP]
> আপনার project-এর যেকোনো জায়গা থেকে `prisma` import করুন:

```typescript
import { prisma } from "./lib/prisma";
```

> [!NOTE]
> যদি আপনার কোনো edge runtime (যেমন Cloudflare Workers, Vercel Edge Functions) থেকে HTTP-এর মাধ্যমে database query করার প্রয়োজন হয়, তাহলে `pg` adapter-এর বদলে Prisma Postgres serverless driver ব্যবহার করুন।

## Step 10 — Environment Config Module

পুরো codebase জুড়ে সরাসরি `process.env` অ্যাক্সেস করার বদলে, সমস্ত environment variable একটি single typed config file-এ কেন্দ্রীভূত করুন। এতে আপনার configuration manage এবং refactor করা সহজ হয়।

📄 `src/config/index.ts`

```typescript
import dotenv from "dotenv";
import path from "path";

dotenv.config({ path: path.join(process.cwd(), ".env") });

export default {
  port: process.env.PORT,
  appUrl: process.env.APP_URL,
  databaseUrl: process.env.DATABASE_URL,
  bcryptSaltRounds: process.env.BCRYPT_SALT_ROUNDS,
  jwtAccessSecret: process.env.JWT_SECRET,
  jwtRefreshSecret: process.env.JWT_REFRESH_SECRET,
  jwtAccessExpireIn: process.env.JWT_ACCESS_EXPIRE_IN,
  jwtRefreshExpireIn: process.env.JWT_REFRESH_EXPIRE_IN,
};
```

### 🔍 Config Key রেফারেন্স

| Key                  | Environment Variable    | উদ্দেশ্য                                              |
| --------------------- | ------------------------- | ------------------------------------------------------ |
| `port`                | `PORT`                    | যে port-এ আপনার Express server listen করে               |
| `appUrl`              | `APP_URL`                 | আপনার application-এর base URL                      |
| `databaseUrl`         | `DATABASE_URL`            | Prisma-এর জন্য PostgreSQL connection string                |
| `bcryptSaltRounds`    | `BCRYPT_SALT_ROUNDS`      | password hash করার সময় ব্যবহৃত salt round-এর সংখ্যা      |
| `jwtAccessSecret`     | `JWT_SECRET`               | JWT access token sign করার জন্য secret key               |
| `jwtRefreshSecret`    | `JWT_REFRESH_SECRET`      | JWT refresh token sign করার জন্য secret key               |
| `jwtAccessExpireIn`   | `JWT_ACCESS_EXPIRE_IN`    | JWT access token-এর expiry সময়কাল (যেমন `15m`)      |
| `jwtRefreshExpireIn`  | `JWT_REFRESH_EXPIRE_IN`   | JWT refresh token-এর expiry সময়কাল (যেমন `7d`)      |

> [!IMPORTANT]
> server চালু করার আগে নিশ্চিত করুন যে এই সব key আপনার `.env` file-এ define করা আছে। কোনো value বাদ পড়লে runtime-এ `undefined` দেখাবে।

📄 `.env` — সম্পূর্ণ উদাহরণ:

```env
PORT=5000
APP_URL=http://localhost:5000
DATABASE_URL="postgres://your-connection-string-here"
BCRYPT_SALT_ROUNDS=12
JWT_SECRET=your-access-token-secret
JWT_REFRESH_SECRET=your-refresh-token-secret
JWT_ACCESS_EXPIRE_IN=15m
JWT_REFRESH_EXPIRE_IN=7d
```

> [!TIP]
> সরাসরি `process.env` ব্যবহার না করে, আপনার প্রজেক্টের যেকোনো জায়গা থেকে config import করুন:

```typescript
import config from "./config";

const port = config.port;
```

## Step 11 — Express App তৈরি করা

Express application instance তৈরি করুন এবং সব global middleware কনফিগার করুন। app configuration এবং server startup-এর মধ্যে পরিষ্কার আলাদা রাখতে এই file-টি server entry point থেকে আলাদা রাখা হয়েছে।

📄 `src/app.ts`

```typescript
import express, { Application, Request, Response } from "express";
import cookieParser from "cookie-parser";
import cors from "cors";
import config from "./config";

const app: Application = express();

// CORS — allow requests from the configured app URL
app.use(cors({
  origin: config.appUrl,
  credentials: true,
}));

// Body parsers
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Cookie parser
app.use(cookieParser());

// Health check route
app.get("/", (req: Request, res: Response) => {
  res.send("Hello World!");
});

export default app;
```

### 🔍 প্রতিটি অংশ কী করে

| অংশ                                                  | কাজ                                                                                     |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `Application`                                          | Express app instance-এর জন্য TypeScript type                                                |
| `cors({ origin: config.appUrl, ... })`                 | শুধুমাত্র আপনার config-এ `APP_URL`-এ define করা URL থেকে cross-origin request সীমাবদ্ধ করে       |
| `credentials: true`                                    | cross-origin request-এর সাথে cookie এবং authorization header পাঠানোর অনুমতি দেয়               |
| `express.json()`                                       | JSON body-সহ incoming request parse করে এবং `req.body`-তে দেখায়                       |
| `express.urlencoded({ extended: true })`               | URL-encoded form data-সহ incoming request parse করে এবং `req.body`-তে দেখায়             |
| `cookieParser()`                                       | `Cookie` header থেকে cookie parse করে এবং `req.cookies`-এ দেখায়                    |
| `app.get("/")`                                         | server চালু আছে কিনা তা নিশ্চিত করতে একটি basic health check route                                  |
| `export default app`                                   | `server.ts`-এ ব্যবহারের জন্য configured app instance export করে                            |

> [!NOTE]
> route এবং additional middleware (যেমন error handler, rate limiter) এই file-এ `export default app`-এর আগে রেজিস্টার করা উচিত, যাতে `server.ts` শুধু server চালু করার কাজেই সীমাবদ্ধ থাকে।

> [!TIP]
> CORS-এর `credentials: true` option-এর জন্য origin একটি নির্দিষ্ট URL হতে হবে — wildcard `*` হলে চলবে না। `credentials: true`-এর সাথে `*` ব্যবহার করলে browser response reject করে দেবে।

## Step 12 — Server Bootstrap করা

Server entry point তৈরি করুন। এই file database-এর সাথে connect করে এবং Express server চালু করে। startup-এর সময় কিছু ব্যর্থ হলে, এটি gracefully Prisma disconnect করে এবং process exit করে।

📄 `src/server.ts`

```typescript
import app from "./app";
import config from "./config";
import { prisma } from "./lib/prisma";

const PORT = config.port;

async function main() {
  try {
    await prisma.$connect();
    console.log("Connected to Prisma database");
    app.listen(PORT, () => {
      console.log(`Server is running on port ${PORT}`);
    });
  } catch (error) {
    console.error("Error starting server:", error);
    await prisma.$disconnect();
    console.log("Disconnected from Prisma database");
    process.exit(1);
  }
}

main();
```

### 🔍 প্রতিটি অংশ কী করে

| অংশ                            | কাজ                                                                          |
| --------------------------------- | ------------------------------------------------------------------------------------ |
| `import app`                     | `app.ts` থেকে configured Express application instance import করে               |
| `import config`                  | কেন্দ্রীভূত environment config module থেকে port নেয়                    |
| `import { prisma }`              | `lib/prisma.ts` থেকে shared Prisma Client instance import করে                    |
| `prisma.$connect()`              | server request গ্রহণ শুরু করার আগেই স্পষ্টভাবে database connection খোলে |
| `app.listen(PORT, ...)`          | কনফিগার করা port-এ HTTP server চালু করে                                     |
| `prisma.$disconnect()`           | startup ব্যর্থ হলে database connection gracefully বন্ধ করে                        |
| `process.exit(1)`                | failure code সহ process exit করে, যাতে error লক্ষ্যণীয় থাকে           |

> [!TIP]
> নিচের কমান্ড দিয়ে development-এ আপনার server চালান:

```bash
npm run dev
```

সবকিছু ঠিকভাবে কাজ করলে আপনি নিচের দুটি message দেখতে পাবেন:

```text
Connected to Prisma database
Server is running on port 5000
```

## Step 13 — package.json Scripts কনফিগার করা

project metadata, script define করতে এবং সব dependency ঠিকমতো আছে কিনা তা নিশ্চিত করতে আপনার `package.json` আপডেট করুন।

📄 `package.json`

```json
{
  "name": "prisma-express",
  "version": "1.0.0",
  "description": "A Prisma Express backend",
  "author": "Rayhan",
  "license": "ISC",
  "main": "dist/server.js",
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js"
  },
  "type": "module",
  "private": true,
  "devDependencies": {
    "@types/bcrypt": "^6.0.0",
    "@types/cookie-parser": "^1.4.10",
    "@types/cors": "^2.8.19",
    "@types/express": "^5.0.6",
    "@types/jsonwebtoken": "^9.0.10",
    "@types/node": "^22.0.0",
    "@types/pg": "^8.20.0",
    "prisma": "^7.8.0",
    "tsx": "^4.22.5",
    "typescript": "^5.5.0"
  },
  "dependencies": {
    "@prisma/adapter-pg": "^7.8.0",
    "@prisma/client": "^7.8.0",
    "bcrypt": "^6.0.0",
    "cookie-parser": "^1.4.7",
    "cors": "^2.8.6",
    "dotenv": "^16.4.5",
    "express": "^5.2.1",
    "http-status": "^2.1.0",
    "jsonwebtoken": "^9.0.3",
    "pg": "^8.22.0"
  }
}
```

### 🔍 Script রেফারেন্স

| Script          | Command                    | উদ্দেশ্য                                                              |
| ----------------- | ---------------------------- | ------------------------------------------------------------------------ |
| `npm run dev`     | `tsx watch src/server.ts`   | hot reload সহ dev server চালু করে — file পরিবর্তনে restart হয়        |
| `npm run build`   | `tsc`                        | TypeScript source file গুলোকে compile করে `dist/`-এর ভেতরে JavaScript-এ পরিণত করে         |
| `npm run start`   | `node dist/server.js`       | `dist/` থেকে compiled production build চালায়                         |

> [!NOTE]
> `tsx watch` development-এ ব্যবহার করা হয়, যাতে আলাদা compilation step ছাড়াই দ্রুত TypeScript execution এবং file পরিবর্তনে স্বয়ংক্রিয় restart পাওয়া যায়।

> [!WARNING]
> `npm run start`-এর আগে সবসময় `npm run build` চালান। `start` script `dist/` থেকে compiled `.js` output চালায় — `dist/` না থাকলে বা পুরনো থাকলে এটি ব্যর্থ হবে।

> [!TIP]
> `package.json`-এর `"type": "module"` field Node.js-কে সব `.js` file-কে ES Modules হিসেবে treat করতে বলে, যা Step 3-এ কনফিগার করা ESM-compatible `tsconfig.json`-এর জন্য প্রয়োজনীয়।

<div align="center">

শেখার উদ্দেশ্যে তৈরি 🎓 | Full-Stack Development — Backend Setup Module

</div>
