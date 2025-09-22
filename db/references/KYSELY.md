# What is Kysely?

Kysely is a **type-safe SQL query builder** for TypeScript. It allows us to:
- Write SQL queries in TypeScript with full type inference.
- Catch schema errors at **compile time** instead of runtime.
- Interact in a direct, yet type-safe way, with the database without an ORM abstraction
- Use **migrations + codegen** to keep application code aligned with the actual database schema.

Instead of writing raw SQL queries in the codebase, we can write:
```const users = await db
  .selectFrom('users')
  .select(['id', 'email', 'name'])
  .where('active', '=', true)
  .execute();
```
And get full IntelliSense + type checking.

## Quickstart
Create an `.env` file with DATABASE_URL=... (ask project manager for URL)

## Connection Pooling

***Note:** Within this repo, the proper connection pooling is already set up, feel free to read the following for more insight. See `db/kysely/client.ts`*

Without pooling, every query opens a new database connection. This can easily overwhelm Postgres and cause the dreaded **“too many connections”** error, stopping anymore database queries.

Kysely doesn’t handle pooling itself; instead, it integrates with drivers that do (e.g. `pg`).

### How Pooling Works
- A pool creates a **limited set of connections** (e.g., 10–20) that are reused across queries.
- Each request borrows a connection, runs queries, then releases it back to the pool.
- This ensures we don’t open hundreds of connections in production

### Example Setup with PostgreSQL in Typescript
```
const db = new Kysely<DB>({
  dialect: new PostgresDialect({
    pool: new Pool({
      connectionString: process.env.DATABASE_URL,
      max: 10, // number of connections in pool
      idleTimeoutMillis: 30000, // release idle connections
    }),
  }),
});

```

# Kysely Codegen
When the database schema changes, Kysely needs updated types. Instead of writing them by hand, we generate them automatically from the database.

## Run kysely-codegen
1. Set database environment variable in terminal:

`export DATABASE_URL=<DATABASE_URL>`

2. Run command: ```npm run kysely:generate```

This interfaces with the `.kysely-codegenrc.json` config file

This connects to your database, introspects the schema and generates `types.ts` like: 
```
export interface DB {
  users: {
    id: string;
    email: string;
    name: string;
    created_at: Date;
  };
  sensor_readings: {
    id: string;
    reading_ts: Date;
    value: number;
  };
}

```

3. Use the generated types

***Note:** Within this repo, the proper connection pooling and type-interfacing is already set up in `db/kysely/client.ts`*

```
import { DB } from '../types';
const db = new Kysely<DB>({ dialect: new PostgresDialect({ pool }) });
```

Now every query is checked against the real schema. 
