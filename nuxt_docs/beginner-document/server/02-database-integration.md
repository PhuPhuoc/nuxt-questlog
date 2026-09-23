# Database Integration - Kết Nối Database

> **Mục tiêu:** Học cách kết nối database trong Nuxt server routes.

## Mục lục

1. [Database Options](#1-database-options)
2. [Prisma ORM](#2-prisma-orm)
3. [Drizzle ORM](#3-drizzle-orm)
4. [Direct SQL](#4-direct-sql)

---

## 1. Database Options

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DATABASE OPTIONS FOR NUXT                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ORM/Query Builder:                                                │
│  ├── Prisma - Popular, type-safe, visual studio                   │
│  ├── Drizzle - Lightweight, SQL-like, fast                        │
│  └── Kysely - Type-safe SQL query builder                         │
│                                                                     │
│  Databases:                                                        │
│  ├── PostgreSQL - Recommended                                      │
│  ├── MySQL - Common                                               │
│  ├── SQLite - Simple, file-based                                  │
│  └── MongoDB - Document database                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Prisma ORM

### 2.1 Cài đặt

```bash
npm install prisma @prisma/client
npx prisma init
```

### 2.2 Schema

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  createdAt DateTime @default(now())
  posts     Post[]
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
}
```

### 2.3 Client Instance

```typescript
// server/utils/db.ts
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

export { prisma }
```

### 2.4 Sử dụng trong API

```typescript
// server/api/users/index.get.ts
import { prisma } from '~/server/utils/db'

export default defineEventHandler(async () => {
  const users = await prisma.user.findMany({
    include: { posts: true }
  })
  return users
})
```

```typescript
// server/api/users/index.post.ts
import { prisma } from '~/server/utils/db'

export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  
  const user = await prisma.user.create({
    data: {
      name: body.name,
      email: body.email
    }
  })
  
  return user
})
```

```typescript
// server/api/users/[id].get.ts
import { prisma } from '~/server/utils/db'

export default defineEventHandler(async (event) => {
  const id = Number(getRouterParam(event, 'id'))
  
  const user = await prisma.user.findUnique({
    where: { id },
    include: { posts: true }
  })
  
  if (!user) {
    throw createError({
      statusCode: 404,
      statusMessage: 'User not found'
    })
  }
  
  return user
})
```

---

## 3. Drizzle ORM

### 3.1 Cài đặt

```bash
npm install drizzle-orm better-sqlite3
npm install -D drizzle-kit
```

### 3.2 Schema

```typescript
// server/db/schema.ts
import { sqliteTable, text, integer } from 'drizzle-orm/sqlite-core'

export const users = sqliteTable('users', {
  id: integer('id').primaryKey({ autoIncrement: true }),
  name: text('name').notNull(),
  email: text('email').notNull().unique()
})

export const posts = sqliteTable('posts', {
  id: integer('id').primaryKey({ autoIncrement: true }),
  title: text('title').notNull(),
  content: text('content'),
  authorId: integer('author_id').references(() => users.id)
})
```

### 3.3 Client

```typescript
// server/db/index.ts
import Database from 'better-sqlite3'
import { drizzle } from 'drizzle-orm/better-sqlite3'
import * as schema from './schema'

const sqlite = new Database('sqlite.db')
export const db = drizzle(sqlite, { schema })
```

### 3.4 Sử dụng

```typescript
// server/api/users/index.get.ts
import { db } from '~/server/db'
import { users } from '~/server/db/schema'

export default defineEventHandler(async () => {
  const allUsers = await db.select().from(users)
  return allUsers
})
```

---

## 4. Direct SQL

### 4.1 Với better-sqlite3

```typescript
// server/utils/db.ts
import Database from 'better-sqlite3'
const db = new Database('sqlite.db')

export { db }
```

```typescript
// server/api/users/index.get.ts
import { db } from '~/server/utils/db'

export default defineEventHandler(async () => {
  const users = db.prepare('SELECT * FROM users').all()
  return users
})
```

```typescript
// server/api/users/index.post.ts
import { db } from '~/server/utils/db'

export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  
  const stmt = db.prepare(`
    INSERT INTO users (name, email) VALUES (?, ?)
  `)
  
  const result = stmt.run(body.name, body.email)
  
  return { id: result.lastInsertRowid, ...body }
})
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DATABASE INTEGRATION SUMMARY                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  PRISMA:                                                          │
│  ├── Schema-based ORM                                             │
│  ├── Type-safe                                                    │
│  └── Visual studio (prisma studio)                                 │
│                                                                     │
│  DRIZZLE:                                                         │
│  ├── SQL-like syntax                                              │
│  ├── Lightweight                                                  │
│  └── Fast migrations                                              │
│                                                                     │
│  BEST PRACTICES:                                                  │
│  ├── Dùng singleton cho DB connection                             │
│  ├── Validate input                                               │
│  └── Handle errors                                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tham Khảo

→ [Prisma Docs](https://www.prisma.io/docs)
→ [Drizzle Docs](https://orm.drizzle.team)
