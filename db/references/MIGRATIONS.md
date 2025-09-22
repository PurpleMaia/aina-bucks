# Database Migrations: Why & How (GoLang Migrate + PostgreSQL)

**Migrations are version control for your database**. They let us change schema/data safely and predictably across **development, staging**, and **production**.

- **Production**: protects real user data. Changes must be audited, reversible, and staged. We run migrations during deployments with locks/backups.
- **Development**: enables rapid iteration. Devs create migrations locally and verify them against a local DB before opening a PR.
- **Consistency**: every environment applies the same, ordered SQL files so schemas never drift.

Key principles:
- **Feature‑coupled**: ship the migration in the same PR as the code that uses it.
- **Reversible**: provide a down step or a documented rollback plan.

## Install GoLang Migrate
**Note**: We use the standalone CLI (you do not need to write Go code)

**Reference**: https://github.com/golang-migrate/migrate

- **macOS (Homebrew)**

`brew install golang-migrate`

- **Windows (Scoop)**

`scoop install migrate`

## How to Make a Migration

1. Before a risky migration, make a database backup

2. Create migration (with the name of migration being the short description). This will generate/populate `db/migrations` with up & down SQL migration files

`migrate create -ext sql -dir db/migrations -seq <name of migration>`

3. Write the SQL Query you would like to change in UP, then the reverse of that operation in DOWN

4. Set Database environment in your terminal with your Database Connection URL

`export DATABASE_URL=<DATABASE_URL>`

5. Send the migration to your database

`migrate -database "${DATABASE_URL}" -path db/migrations up`

6. Test the rollback

`migrate -database "${DATABASE_URL}" -path db/migrations down 1`

7. After schema changes, regenerate Kysely types. This is so the project understands any database changes to match for type-safety

`npm run kysely:generate`

## Troubleshooting
- **Dirty migration state**: if a migration failed mid‑way, fix the SQL query, force to a working version, then repeat step 5.
    - To find working version, look at migration file names and use the prepended number


**Force to working version command**: `migrate -path packages/db/migrations -database "$DATABASE_URL" force <target_version>`


- **Out‑of‑order files**: rebase your branch, regenerate the migration with a later timestamp.

## Project Conventions
Migrations typically live in the db directory in the project
- The order of migrations is the prepended tag on the file name with the short description

`00010_added-user-table.up.sql`

- We use SQL files (up/down paired files)