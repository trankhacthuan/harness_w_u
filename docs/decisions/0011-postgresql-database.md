# 0011 PostgreSQL Database

Date: 2026-10-12

## Status

Accepted. The choice of PostgreSQL was requested by the project owner; the data
access tooling below was delegated and is open to change by a new decision.

## Context

The site needs real persistence (for example reservations) without depending on
third-party feature APIs. An earlier proposal stored records in a local file;
that cannot persist on serverless hosting and is not a professional baseline.
Reservation data includes names and phone numbers, which are personal data and a
hard gate in `docs/FEATURE_INTAKE.md` (Audit/security).

## Decision

| Concern | Choice |
| --- | --- |
| Database | PostgreSQL 16 or newer (any currently supported major version) |
| Driver | `pg` (node-postgres) |
| Data access | Drizzle ORM (TypeScript, SQL-first) |
| Migrations | `drizzle-kit`; plain SQL files committed under `db/migrations/` |
| Connection | `DATABASE_URL` (the real database) and `TEST_DATABASE_URL` (a separate, disposable database used only by tests) |
| Local development | Docker `postgres` image, a native install, or a hosted instance |
| Admin UI / authentication | Out of scope |

Rules:

1. **Migrations are forward-only and reviewed.** Never edit an applied
   migration. Dropping or rewriting data is a high-risk story (Data model / data
   loss hard gate).
2. **Parse at the boundary.** `DATABASE_URL` and all request payloads are
   validated with a schema before use; the database layer receives typed values.
3. **Parameterized access only.** No string-built SQL.
4. **Least privilege.** The application role is not a superuser and owns no
   more than it needs.
5. **Secrets stay out of git.** `.env.local` is git-ignored; a committed
   `.env.example` holds placeholders only.
6. **Data conventions.** `timestamptz` in UTC, `uuid` primary keys, explicit
   `NOT NULL` / `CHECK` / `UNIQUE` constraints, indexes justified by a query.
7. **Personal data is minimized.** Collect only fields the product uses; never
   write personal data to logs. The retention period is an open question
   (see Follow-Up) and must be decided by the owner, not assumed.
8. **Tests use a real, separate database.** Integration tests run against the
   database named by `TEST_DATABASE_URL`, which is created, migrated, and wiped
   by the tests. Mocks do not count as database proof. The test runner must
   **abort** if `TEST_DATABASE_URL` is unset or resolves to the same database as
   `DATABASE_URL`, so tests can never touch real reservation data.
9. **The site must not depend on the database to start.** `DATABASE_URL` is
   validated lazily, the first time the data layer is used — never at process
   start or module import. Pages that need no data must not import the database
   module, and no page queries the database during `next build` or static
   generation. A missing or unreachable database only disables the features
   that need it.
10. **Fail visibly and kindly.** When the database is unavailable, the API
    returns `503` with a stable error shape; the reservation form shows a clear
    message and a call button (`tel:`) instead of failing silently or crashing.
11. **Unverified is a real status.** Until the integration tests have run
    against a real PostgreSQL instance, the story that depends on it is
    reported as **not verified** and stays open. Code written without a
    database to run against is allowed; claiming it works is not.

Reading data back (for staff) is done with a SQL client or an export script. A
web admin screen needs authentication and is a separate decision.

## Alternatives Considered

1. Local JSON/JSONL file — rejected: no durability guarantees, no constraints,
   and unusable on serverless hosting.
2. SQLite — workable for a single node, but native-module friction on Windows
   and no network access for managed hosting; PostgreSQL was explicitly requested.
3. Prisma instead of Drizzle — equally professional; Drizzle chosen for
   SQL-visible migrations and a lighter runtime. Switching needs a new decision.

## Consequences

Positive:

- Durable, constrained, queryable data; works with serverless and traditional
  hosting through a connection string.
- Migrations are reviewable SQL in git.

Tradeoffs:

- A PostgreSQL instance is required to run the booking flow and integration
  tests; one more moving part than a file.
- Without email or messaging APIs the restaurant is not notified of new
  reservations; they must read them from the database.
- Backups, retention, and access control become operational duties of whoever
  hosts the database.

## Follow-Up

- Owner decides the personal-data retention period: `[CẦN QUYẾT ĐỊNH: thời hạn
  lưu thông tin đặt bàn]`.
- Owner decides the hosting target (database and app); record it as its own
  decision when the first deploy story is picked.
- First data story creates `.env.example`, `db/`, the Drizzle config, and the
  `db:*` scripts named in `docs/HARNESS.md`.
