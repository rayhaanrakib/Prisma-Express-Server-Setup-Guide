<!-- Header Badges -->
<div align="center">

# 🛠️ Backend (Prisma-Express) Server Setup Guide

![Prisma](https://img.shields.io/badge/Prisma-ORM-2D3748?style=for-the-badge&logo=prisma&logoColor=white)
![Bun](https://img.shields.io/badge/Bun-Package_Manager-FBF0DF?style=for-the-badge&logo=bun&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-ESM-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Express](https://img.shields.io/badge/Express-Framework-000000?style=for-the-badge&logo=express&logoColor=white)

A step-by-step reference guide for setting up a **TypeScript backend** with **Express**, **Prisma ORM**, and **Bun** as the package manager.

📖 [Official Prisma Docs](https://www.prisma.io/docs/prisma-postgres/quickstart/prisma-orm#1-create-a-new-project)

</div>

---

## 📋 Table of Contents

- [🛠️ Backend Setup Guide](#️-backend-setup-guide)
  - [📋 Table of Contents](#-table-of-contents)
  - [⚡ Quick Reference](#-quick-reference)
  - [Step 1 — Project Initialization](#step-1--project-initialization)
  - [Step 2 — Install Dependencies](#step-2--install-dependencies)
  - [Step 3 — Configure TypeScript (ESM)](#step-3--configure-typescript-esm)
  - [Step 4 — Initialize Prisma](#step-4--initialize-prisma)
  - [Step 5 — Setup Environment Variable](#step-5--setup-environment-variable)
  - [Step 6 — Prisma Schema Strategy](#step-6--prisma-schema-strategy)
  - [Step 7 — Database Migration](#step-7--database-migration)
  - [Step 8 — Generate Prisma Client](#step-8--generate-prisma-client)
  - [Step 9 — Instantiate Prisma Client](#step-9--instantiate-prisma-client)
  - [Step 10 — Environment Config Module](#step-10--environment-config-module)
  - [Step 11 — Create the Express App](#step-11--create-the-express-app)
  - [Step 12 — Bootstrap the Server](#step-12--bootstrap-the-server)
  - [Step 13 — Configure package.json Scripts](#step-13--configure-packagejson-scripts)

---

## ⚡ Quick Reference

> Jump straight to the commands if you already know the setup.

| #   | Task                     | Command                      |
| --- | ------------------------ | ---------------------------- |
| 1   | Initialize Prisma        | `bunx --bun prisma init`     |
| 2   | Run a Migration          | `bunx prisma migrate dev`    |
| 3   | Generate Prisma Client   | `bunx --bun prisma generate` |
| 4   | Open Prisma Studio (GUI) | `bunx prisma studio`         |
| 5   | Deploy (Production Only) | `bunx prisma migrate deploy` |
| 6   | Start Dev Server         | `bun run dev`                |
| 7   | Build for Production     | `bun run build`              |
| 8   | Start Production Server  | `bun run start`              |

---

## Step 1 — Project Initialization

Create and set up your project folder:

```bash
mkdir your-project-name
cd your-project-name
```

Initialize Git and create a `.gitignore`:

```bash
git init
```

📄 `.gitignore`

```
node_modules
.env
```

> [!IMPORTANT]
> Always add `.env` to `.gitignore` to avoid exposing your database credentials.

Initialize a Bun project:

```bash
bun init -y
```

---

## Step 2 — Install Dependencies

### 🔧 Core Tooling

📦 **Dev Dependencies** — TypeScript tooling + Prisma CLI:

```bash
bun add -d typescript tsx @types/node prisma
```

📦 **Runtime Dependencies** — Prisma Client + PostgreSQL adapter + dotenv:

```bash
bun add @prisma/client @prisma/adapter-pg dotenv
```

Initialize TypeScript config:

```bash
bunx tsc --init
```

> [!NOTE]
> Bun automatically loads `.env` files, so `import 'dotenv/config'` is not required. Remove it if it appears in any generated files.

---

### 🌐 Express Server & Middleware

📦 **Runtime Dependencies** — Express + middleware packages:

```bash
bun add express cors jsonwebtoken cookie-parser http-status bcrypt
```

📦 **Dev Dependencies** — TypeScript type definitions:

```bash
bun add -d @types/express @types/cors @types/jsonwebtoken @types/cookie-parser @types/bcrypt
```

#### 📖 Package Descriptions

**Runtime Packages:**

| Package         | Description                                                                                                                  |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `express`       | Minimal and flexible Node.js web framework for building APIs and handling HTTP requests/responses                            |
| `cors`          | Middleware that enables **Cross-Origin Resource Sharing**, allowing your API to accept requests from different domains/ports |
| `jsonwebtoken`  | Used to **sign and verify JWTs** (JSON Web Tokens) for authentication and authorization                                      |
| `cookie-parser` | Middleware that **parses cookies** attached to incoming requests, making them accessible via `req.cookies`                   |
| `http-status`   | Provides **readable HTTP status code constants** (e.g., `httpStatus.OK` instead of `200`)                                   |
| `bcrypt`        | Used to **hash and compare passwords** securely before storing them in the database                                          |

**Dev Packages (Type Definitions):**

| Package                | Description                                   |
| ---------------------- | --------------------------------------------- |
| `@types/express`       | TypeScript type definitions for Express       |
| `@types/cors`          | TypeScript type definitions for cors          |
| `@types/jsonwebtoken`  | TypeScript type definitions for jsonwebtoken  |
| `@types/cookie-parser` | TypeScript type definitions for cookie-parser |
| `@types/bcrypt`        | TypeScript type definitions for bcrypt        |

> [!NOTE]
> `http-status` ships with its own type definitions, so no separate `@types/http-status` package is needed.

---

### 🐘 PostgreSQL Client

📦 **Runtime Dependencies** — PostgreSQL client for Node.js:

```bash
bun add pg
```

📦 **Dev Dependencies** — TypeScript type definitions:

```bash
bun add -d @types/pg
```

#### 📖 Package Descriptions

| Package     | Type    | Description                                                                                       |
| ----------- | ------- | ------------------------------------------------------------------------------------------------- |
| `pg`        | Runtime | The **PostgreSQL client for Node.js** — allows direct communication with your PostgreSQL database |
| `@types/pg` | Dev     | TypeScript type definitions for `pg`                                                              |

> [!NOTE]
> `pg` is the underlying driver that `@prisma/adapter-pg` depends on to communicate with PostgreSQL. Installing it explicitly gives you direct access to the PostgreSQL client when needed outside of Prisma.

---

## Step 3 — Configure TypeScript (ESM)

Replace the contents of your `tsconfig.json` with the following for **ESM compatibility**:

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
> Uncomment `"include": ["src/**/*"]` once you move your source files into a `/src` directory.

---

## Step 4 — Initialize Prisma

Run the Prisma init command with a **custom client output path**:

```bash
bunx --bun prisma init --output ../generated/prisma
```

✅ This command automatically generates:

| Generated Item         | Description                               |
| ---------------------- | ----------------------------------------- |
| `prisma/schema.prisma` | Your database schema definition file      |
| `.env`                 | Root-level file for environment variables |
| `prisma.config.ts`     | Prisma configuration file                 |

> [!IMPORTANT]
> Always use `bunx --bun` (not just `bunx`) for Prisma commands. The `--bun` flag forces the command to run on Bun's runtime instead of Node.js.

---

### 📄 Generated File Contents

After running `prisma init`, the following files are generated with these default contents:

**`prisma.config.ts`** *(default — will be updated in Step 6)*

```ts
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

**`prisma/schema.prisma`** *(default — structure depends on schema strategy chosen in Step 6)*

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
> The schema uses the **ESM-first** `prisma-client` generator with a custom output path pointing to `../generated/prisma`. This keeps the generated client outside the `prisma/` directory for cleaner imports.

---

## Step 5 — Setup Environment Variable

1. Create a **Prisma Postgres** database via the [Prisma Console](https://console.prisma.io)
2. Copy your connection string from the CLI output
3. Replace the placeholder value in your `.env` file:

```env
DATABASE_URL="postgres://your-connection-string-here"
```

> [!CAUTION]
> Never commit your `.env` file to Git. It contains sensitive credentials.

---

## Step 6 — Prisma Schema Strategy

Before writing your models, decide how you want to organize your Prisma schema. Prisma supports both a **single-file** and a **multi-file** approach.

---

### 📁 Option A — Single File Schema *(simple projects)*

For small projects with around **5–10 models**, keeping everything in one file is the simplest and most maintainable option.

**Directory structure:**

```
prisma/
├── migrations/
└── schema.prisma
```

No changes are needed to `prisma.config.ts` — the default generated config already points to `prisma/schema.prisma` and works out of the box.

---

### 📂 Option B — Multi-File Schema *(recommended for medium to large projects)*

If your project has **15+ models**, multiple developers, or clearly separated domains (users, products, orders, payments, etc.), splitting the schema across multiple files improves organization and maintainability.

**Directory structure:**

```
prisma/
├── migrations/
├── schema/
│   ├── schema.prisma       ← generator + datasource block lives here
│   ├── user.prisma
│   ├── product.prisma
│   ├── order.prisma
│   ├── payment.prisma
│   └── review.prisma
```

> [!NOTE]
> Using a multi-file Prisma schema is considered a **professional and scalable approach** for medium and large projects. Whether it is the best choice depends on the size and complexity of your application.

#### ✏️ Update `prisma.config.ts` for Multi-File Schema

When using the multi-file approach, update the `schema` property in `prisma.config.ts` to point to the **directory** instead of a single file. Also add the optional `seed` script path:

📄 `prisma.config.ts`

```ts
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

#### 🔍 What Changed from the Default Config

| Property             | Default Value             | Updated Value       | Reason                                                        |
| -------------------- | ------------------------- | ------------------- | ------------------------------------------------------------- |
| `schema`             | `"prisma/schema.prisma"`  | `"prisma/schema"`   | Points to a **directory** instead of a single file           |
| `migrations.seed`    | *(not set)*               | `"tsx prisma/seed.ts"` | Registers a seed script to populate the database with initial data |

> [!TIP]
> The `generator client` block and `datasource db` block must live in **one** of the `.prisma` files inside the schema directory — conventionally in `schema/schema.prisma` or a dedicated `schema/base.prisma`.

> [!WARNING]
> Do **not** mix the single-file and multi-file approaches. If `schema` points to a directory, Prisma expects **all** `.prisma` files inside that directory to form the complete schema — there should be no `schema.prisma` at the `prisma/` root level.

---

You are now ready to write your schema models. ✅

---

## Step 7 — Database Migration

> 📖 [Prisma Migrate Docs](https://www.prisma.io/docs/orm/prisma-migrate/getting-started)

Once your models are defined in your schema file(s), create and apply a migration:

```bash
bunx prisma migrate dev
```

You will be prompted to name the migration. Example:

```bash
✔ Enter a name for the new migration: › init
```

> [!WARNING]
> `migrate dev` is a **development-only** command. For production environments, use:
>
> ```bash
> bunx prisma migrate deploy
> ```

---

## Step 8 — Generate Prisma Client

After migrating, manually generate the Prisma Client:

```bash
bunx --bun prisma generate
```

> [!IMPORTANT]
> **Prisma v7+ Breaking Change:** `migrate dev` no longer auto-triggers `prisma generate`. You must **always run this step explicitly** after every migration or schema change.

> [!TIP]
> Re-run `bunx --bun prisma generate` any time you modify your schema file(s) to keep your Prisma Client in sync.

---

## Step 9 — Instantiate Prisma Client

Create a dedicated file to instantiate and export the Prisma Client. This keeps your database connection centralized and reusable across your project.

📄 `src/lib/prisma.ts`

```ts
import "dotenv/config";
import { PrismaPg } from "@prisma/adapter-pg";
import { PrismaClient } from "../generated/prisma/client";

const connectionString = `${process.env.DATABASE_URL}`;
const adapter = new PrismaPg({ connectionString });
const prisma = new PrismaClient({ adapter });

export { prisma };
```

#### 🔍 What Each Line Does

| Line                                 | Purpose                                                         |
| ------------------------------------ | --------------------------------------------------------------- |
| `import "dotenv/config"`             | Loads environment variables from `.env` into `process.env`     |
| `PrismaPg`                           | The **PostgreSQL driver adapter** that bridges Prisma and `pg`  |
| `PrismaClient`                       | The auto-generated Prisma Client from your schema              |
| `new PrismaPg({ connectionString })` | Creates a `pg`-based adapter using your `DATABASE_URL`         |
| `new PrismaClient({ adapter })`      | Instantiates Prisma Client with the PostgreSQL adapter injected |
| `export { prisma }`                  | Exports the client instance for use throughout your application |

> [!TIP]
> Import `prisma` from this file anywhere in your project:
>
> ```ts
> import { prisma } from "./lib/prisma";
> ```

> [!NOTE]
> If you need to query your database via HTTP from an **edge runtime** (e.g., Cloudflare Workers, Vercel Edge Functions), use the [Prisma Postgres serverless driver](https://www.prisma.io/docs/orm/prisma-client/setup-and-configuration/driver-adapters) instead of the `pg` adapter.

---

## Step 10 — Environment Config Module

Instead of accessing `process.env` directly throughout your codebase, centralize all environment variables in a single typed config file. This makes your configuration easier to manage and refactor.

📄 `src/config/index.ts`

```ts
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

#### 🔍 Config Keys Reference

| Key                  | Environment Variable    | Purpose                                             |
| -------------------- | ----------------------- | --------------------------------------------------- |
| `port`               | `PORT`                  | The port your Express server listens on             |
| `appUrl`             | `APP_URL`               | The base URL of your application                    |
| `databaseUrl`        | `DATABASE_URL`          | PostgreSQL connection string for Prisma             |
| `bcryptSaltRounds`   | `BCRYPT_SALT_ROUNDS`    | Number of salt rounds used when hashing passwords   |
| `jwtAccessSecret`    | `JWT_SECRET`            | Secret key for signing JWT access tokens            |
| `jwtRefreshSecret`   | `JWT_REFRESH_SECRET`    | Secret key for signing JWT refresh tokens           |
| `jwtAccessExpireIn`  | `JWT_ACCESS_EXPIRE_IN`  | Expiry duration for JWT access tokens (e.g. `15m`) |
| `jwtRefreshExpireIn` | `JWT_REFRESH_EXPIRE_IN` | Expiry duration for JWT refresh tokens (e.g. `7d`) |

> [!IMPORTANT]
> Make sure all of these keys are defined in your `.env` file before starting the server. Missing values will result in `undefined` at runtime.

📄 `.env` — full example:

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
> Import config anywhere in your project instead of using `process.env` directly:
>
> ```ts
> import config from "./config";
>
> const port = config.port;
> ```

---

## Step 11 — Create the Express App

Create the Express application instance and configure all global middleware. This file is kept separate from the server entry point to maintain a clean separation between **app configuration** and **server startup**.

📄 `src/app.ts`

```ts
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

#### 🔍 What Each Part Does

| Part                                     | Purpose                                                                              |
| ---------------------------------------- | ------------------------------------------------------------------------------------ |
| `Application`                            | TypeScript type for the Express app instance                                         |
| `cors({ origin: config.appUrl, ... })`   | Restricts cross-origin requests to only the URL defined in `APP_URL` from your config |
| `credentials: true`                      | Allows cookies and authorization headers to be sent with cross-origin requests       |
| `express.json()`                         | Parses incoming requests with a **JSON body** and exposes it on `req.body`           |
| `express.urlencoded({ extended: true })` | Parses incoming requests with **URL-encoded form data** and exposes it on `req.body` |
| `cookieParser()`                         | Parses cookies from the `Cookie` header and exposes them on `req.cookies`            |
| `app.get("/")`                           | A basic health check route to confirm the server is running                          |
| `export default app`                     | Exports the configured app instance to be consumed by `server.ts`                    |

> [!NOTE]
> Routes and additional middleware (e.g., error handlers, rate limiters) should be registered in this file **before** `export default app`, keeping `server.ts` focused solely on starting the server.

> [!TIP]
> The `credentials: true` option in CORS **requires** the `origin` to be a specific URL — not a wildcard `*`. Using `*` with `credentials: true` will cause browsers to reject the response.

---

## Step 12 — Bootstrap the Server

Create the server entry point. This file connects to the database and starts the Express server. If anything fails during startup, it gracefully disconnects Prisma and exits the process.

📄 `src/server.ts`

```ts
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

#### 🔍 What Each Part Does

| Part                    | Purpose                                                                              |
| ----------------------- | ------------------------------------------------------------------------------------ |
| `import app`            | Imports the configured Express application instance from `app.ts`                   |
| `import config`         | Pulls the port from the centralized environment config module                        |
| `import { prisma }`     | Imports the shared Prisma Client instance from `lib/prisma.ts`                      |
| `prisma.$connect()`     | Explicitly opens the database connection before the server starts accepting requests |
| `app.listen(PORT, ...)` | Starts the HTTP server on the configured port                                        |
| `prisma.$disconnect()`  | Gracefully closes the database connection if startup fails                           |
| `process.exit(1)`       | Exits the process with a failure code so the error doesn't go unnoticed              |

> [!TIP]
> Run your server in development with:
>
> ```bash
> bun run dev
> ```
>
> You should see both of these messages if everything is working correctly:
>
> ```
> Connected to Prisma database
> Server is running on port 5000
> ```

---

## Step 13 — Configure package.json Scripts

Update your `package.json` to define the project metadata, scripts, and confirm all dependencies are in place.

📄 `package.json`

```json
{
  "name": "prisma-press",
  "version": "1.0",
  "description": "A Prisma Press",
  "author": "Rayhan",
  "license": "ISC",
  "module": "index.ts",
  "main": "server.ts",
  "scripts": {
    "start": "node dist/server.js",
    "build": "tsc",
    "dev": "tsx watch src/server.ts"
  },
  "type": "module",
  "private": true,
  "devDependencies": {
    "@types/bcrypt": "^6.0.0",
    "@types/bun": "latest",
    "@types/cookie-parser": "^1.4.10",
    "@types/cors": "^2.8.19",
    "@types/express": "^5.0.6",
    "@types/jsonwebtoken": "^9.0.10",
    "@types/node": "^26.1.0",
    "@types/pg": "^8.20.0",
    "prisma": "^7.8.0",
    "tsx": "^4.22.5"
  },
  "peerDependencies": {
    "typescript": "^6.0.3"
  },
  "dependencies": {
    "@prisma/adapter-pg": "^7.8.0",
    "@prisma/client": "^7.8.0",
    "bcrypt": "^6.0.0",
    "cookie-parser": "^1.4.7",
    "cors": "^2.8.6",
    "dotenv": "^17.4.2",
    "express": "^5.2.1",
    "http-status": "^2.1.0",
    "jsonwebtoken": "^9.0.3",
    "pg": "^8.22.0"
  }
}
```

#### 🔍 Scripts Reference

| Script          | Command                   | Purpose                                                              |
| --------------- | ------------------------- | -------------------------------------------------------------------- |
| `bun run dev`   | `tsx watch src/server.ts` | Starts the dev server with **hot reload** — restarts on file changes |
| `bun run build` | `tsc`                     | Compiles TypeScript source files into JavaScript inside `dist/`      |
| `bun run start` | `node dist/server.js`     | Runs the compiled production build from `dist/`                      |

> [!NOTE]
> `tsx watch` is used for development instead of `bun --watch` to ensure compatibility with the TypeScript compiler options configured in `tsconfig.json`.

> [!WARNING]
> Always run `bun run build` before `bun run start`. The `start` script runs the compiled `.js` output from `dist/` — if `dist/` is missing or outdated, it will fail.

> [!TIP]
> The `"type": "module"` field in `package.json` tells Node.js to treat all `.js` files as **ES Modules**, which is required for the ESM-compatible `tsconfig.json` configured in Step 3.

---

<div align="center">

Made for learning purposes 🎓 | Full-Stack Development — Backend Setup Module

</div>
