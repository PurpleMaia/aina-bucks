# Quickstart

## Project Setup
1. Install npm (if you don't have it)

**Reference:** https://docs.npmjs.com/downloading-and-installing-node-js-and-npm

2. Create a new Next.js app

**Reference:** https://nextjs.org/docs/app/getting-started/installation

## Project Database Querying Setup
1. Install **Kysely & Kysely Codegen** (querying language at project level)
- `npm install kysely`
- `npm install --save-dev pg`
- `npm install —-save-dev kysely-codegen`

2. Create runnable script shortcut in `package.json`
```
# Under the scripts object, add this command:
"scripts": {      
    # existing scripts...
    "kysely:generate": "kysely-codegen" <--
  },
```
*whenever you need to rerun Kysely Database types, run this command:* `npm run kysely:generate`

3. Read `KYSELY.md` in `db/references` on more about Kysely

**Reference**: https://kysely.dev/docs/intro

## Database Migrations Setup (Optional)
1. Install GoLang Migrate onto local computer

**macOS (Homebrew)**: `brew install golang-migrate`

**Windows (Scoop)**: `scoop install migrate`

2. Read `MIGRATION.md` in `db/references` on more about database migrations
