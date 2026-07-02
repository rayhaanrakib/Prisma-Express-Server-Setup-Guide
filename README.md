<!-- Header Badges -->
<div align="center">

# 🗄️ Prisma Setup Guide

![Prisma](https://img.shields.io/badge/Prisma-ORM-2D3748?style=for-the-badge&logo=prisma&logoColor=white)
![Bun](https://img.shields.io/badge/Bun-Package_Manager-FBF0DF?style=for-the-badge&logo=bun&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-ESM-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)

A step-by-step reference guide for setting up **Prisma ORM** in a TypeScript project using **Bun** as the package manager.

📖 [Official Prisma Docs](https://www.prisma.io/docs/prisma-postgres/quickstart/prisma-orm#1-create-a-new-project)

</div>

---

## 📋 Table of Contents

- [🗄️ Prisma Setup Guide](#️-prisma-setup-guide)
  - [📋 Table of Contents](#-table-of-contents)
  - [⚡ Quick Reference](#-quick-reference)
  - [Step 1 — Project Initialization](#step-1--project-initialization)
  - [Step 2 — Install Dependencies](#step-2--install-dependencies)
  - [Step 3 — Configure TypeScript (ESM)](#step-3--configure-typescript-esm)
  - [Step 4 — Initialize Prisma](#step-4--initialize-prisma)
  - [Step 5 — Setup Environment Variable](#step-5--setup-environment-variable)
  - [Step 6 — Database Migration](#step-6--database-migration)
  - [Step 7 — Generate Prisma Client](#step-7--generate-prisma-client)

---

## ⚡ Quick Reference

> Jump straight to the commands if you already know the setup.

| #   | Task                      | Command                              |
| --- | ------------------------- | ------------------------------------ |
| 1   | Initialize Prisma         | `bunx --bun prisma init`             |
| 2   | Run a Migration           | `bunx prisma migrate dev`            |
| 3   | Generate Prisma Client    | `bunx --bun prisma generate`         |
| 4   | Open Prisma Studio (GUI)  | `bunx prisma studio`                 |
| 5   | Deploy (Production Only)  | `bunx prisma migrate deploy`         |

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
bunx --bun prisma init --output ./generated/prisma
```

✅ This command automatically generates:

| Generated Item         | Description                                       |
| ---------------------- | ------------------------------------------------- |
| `prisma/schema.prisma` | Your database schema definition file             |
| `.env`                 | Root-level file for environment variables        |
| `prisma.config.ts`     | Prisma configuration file                        |

> [!IMPORTANT]
> Always use `bunx --bun` (not just `bunx`) for Prisma commands. The `--bun` flag forces the command to run on Bun's runtime instead of Node.js.

---

## Step 5 — Setup Environment Variable

1. Create a **Prisma Postgres** database via the [Prisma Console](https://console.prisma.io)
2. Copy your connection string
3. Replace the placeholder value in your `.env` file:

```env
DATABASE_URL="your-prisma-postgres-connection-string-here"
```

> [!CAUTION]
> Never commit your `.env` file to Git. It contains sensitive credentials.

---

## Step 6 — Database Migration

> 📖 [Prisma Migrate Docs](https://www.prisma.io/docs/orm/prisma-migrate/getting-started)

Once your models are defined in `schema.prisma`, create and apply a migration:

```bash
bunx prisma migrate dev
```

You will be prompted to name the migration. Example:

```bash
✔ Enter a name for the new migration: › init
```

> [!WARNING]
> `migrate dev` is a **development-only** command. For production environments, use:
> ```bash
> bunx prisma migrate deploy
> ```

---

## Step 7 — Generate Prisma Client

After migrating, manually generate the Prisma Client:

```bash
bunx --bun prisma generate
```

> [!IMPORTANT]
> **Prisma v7+ Breaking Change:** `migrate dev` no longer auto-triggers `prisma generate`. You must **always run this step explicitly** after every migration or schema change.

> [!TIP]
> Re-run `bunx --bun prisma generate` any time you modify your `schema.prisma` file to keep your Prisma Client in sync.

---

<div align="center">

Made for learning purposes 🎓 | Full-Stack Development — Prisma Backend Module

</div>
