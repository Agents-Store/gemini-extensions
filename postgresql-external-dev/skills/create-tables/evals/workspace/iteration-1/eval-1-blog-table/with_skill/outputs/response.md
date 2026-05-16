# Blog Posts Table -- PostgreSQL for NocoDB External Database

Here is the SQL to create a `blog_posts` table that is fully compatible with NocoDB (primary system) and NocoBase (external data source reader).

## SQL

```sql
CREATE TABLE "public"."blog_posts" (
    "id"         serial       NOT NULL,
    "title"      text,
    "body"       text,
    "created_at" timestamp    DEFAULT now(),
    "updated_at" timestamp    DEFAULT now(),
    PRIMARY KEY ("id")
);
```

## Why this structure

| Decision | Rationale |
|----------|-----------|
| `serial` PK named `id` | NocoDB default for PostgreSQL (`int4` + auto-increment). NocoBase reads `serial` correctly as an external source. Never use UUID or text-based PKs -- NocoDB expects auto-increment integers. |
| `text` for `title` and `body` | `text` is in the safe-types intersection for both NocoDB and NocoBase. `character varying` also works, but `text` is simpler when you do not need a length constraint. |
| `timestamp` (without timezone) for `created_at` / `updated_at` | NocoDB default. Use `timestamptz` only if timezone awareness matters. |
| `DEFAULT now()` on both timestamps | Ensures rows get automatic creation and update timestamps. NocoDB and NocoBase both display these correctly. |

## If you need timezone-aware timestamps

Replace `timestamp` with `timestamptz` -- both systems support it:

```sql
CREATE TABLE "public"."blog_posts" (
    "id"         serial       NOT NULL,
    "title"      text,
    "body"       text,
    "created_at" timestamptz  DEFAULT now(),
    "updated_at" timestamptz  DEFAULT now(),
    PRIMARY KEY ("id")
);
```

## If you expect a large number of posts

Use `bigserial` instead of `serial` for IDs beyond the `int4` range (~2.1 billion):

```sql
CREATE TABLE "public"."blog_posts" (
    "id"         bigserial    NOT NULL,
    "title"      text,
    "body"       text,
    "created_at" timestamp    DEFAULT now(),
    "updated_at" timestamp    DEFAULT now(),
    PRIMARY KEY ("id")
);
```

Both `serial` and `bigserial` are compatible with NocoDB and NocoBase. If you later add foreign keys referencing this table, the FK column type must match: `int4` for `serial`, `int8` for `bigserial`.

## Types to avoid

Do not use `ARRAY[]`, `ENUM`, `jsonb`, `POINT`, `POLYGON`, `PATH`, `CIRCLE`, or `INHERITS` -- these are not supported by the NocoDB + NocoBase intersection.
