# One-to-One Relationship: users and profiles (NocoBase External Database)

For a NocoBase external database, a one-to-one relationship is implemented as a regular foreign key with a `UNIQUE` constraint on the FK column. This is what tells NocoDB (and NocoBase when it reads the external schema) that the relationship is one-to-one rather than one-to-many.

## SQL

```sql
-- 1. Create the parent table (users)
CREATE TABLE "public"."users" (
    "id"         serial    NOT NULL,
    "username"   text,
    "created_at" timestamp DEFAULT now(),
    "updated_at" timestamp DEFAULT now(),
    PRIMARY KEY ("id")
);

-- 2. Create the child table (profiles) with UNIQUE on the FK column
CREATE TABLE "public"."profiles" (
    "id"         serial    NOT NULL,
    "user_id"    int4      UNIQUE,            -- UNIQUE = One-to-One
    "bio"        text,
    "avatar_url" text,
    "created_at" timestamp DEFAULT now(),
    "updated_at" timestamp DEFAULT now(),
    PRIMARY KEY ("id")
);

-- 3. FK constraint (NocoDB style)
ALTER TABLE "public"."profiles"
    ADD CONSTRAINT "fk_users_profiles"
    FOREIGN KEY ("user_id")
    REFERENCES "public"."users" ("id")
    ON DELETE NO ACTION
    ON UPDATE NO ACTION;

-- 4. Index on the FK column
CREATE INDEX "idx_profiles_user_id" ON "public"."profiles" ("user_id");
```

## What makes this a one-to-one instead of one-to-many

The only structural difference from a one-to-many relationship is the `UNIQUE` constraint on `user_id` in the `profiles` table. Without `UNIQUE`, multiple profiles could reference the same user, making it one-to-many. With `UNIQUE`, PostgreSQL enforces that each `user_id` value can appear at most once in the `profiles` table, guaranteeing exactly one profile per user.

NocoDB identifies one-to-one relationships specifically by detecting this `UNIQUE` constraint on the FK column. NocoBase reads the same physical schema without conflicts.

## Key points

- **FK column type must match the parent PK type**: `user_id` is `int4` because `users.id` is `serial` (which is `int4`). If you used `bigserial` for the parent PK, the FK column would need to be `int8`.
- **ON DELETE NO ACTION ON UPDATE NO ACTION**: This is the NocoDB default. Deleting a user is blocked if a profile record still references it. Both NocoDB and NocoBase correctly read this behavior.
- **The explicit index** (`idx_profiles_user_id`) is technically redundant because PostgreSQL automatically creates a unique index for the `UNIQUE` constraint. However, adding it explicitly does no harm and makes the intent clear.
- **Naming convention**: The FK constraint follows the pattern `fk_{parent}_{child}` and the index follows `idx_{table}_{column}`.

## Relation checklist

- [x] FK column type matches parent PK type (`int4` for `serial`)
- [x] FK constraint uses `ON DELETE NO ACTION ON UPDATE NO ACTION`
- [x] Index created on the FK column
- [x] `UNIQUE` constraint on the FK column (this is what makes it one-to-one)
