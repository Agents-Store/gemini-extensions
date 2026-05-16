# Diagnosis: ENUM Types, ARRAY Columns, and UUID Primary Keys in NocoDB

You hit three of the most common incompatibilities when connecting a PostgreSQL external database to NocoDB. Here is what went wrong and how to fix each one.

---

## 1. ENUM Types Are Not Supported

**Problem:** PostgreSQL `ENUM` types (e.g., `CREATE TYPE status_enum AS ENUM ('draft', 'active', 'archived')`) are not recognized by NocoDB. NocoDB cannot map custom PostgreSQL types to its internal field model, so ENUM columns will either not appear, show errors, or behave unpredictably.

**Fix:** Replace every `ENUM` column with plain `text`, then configure SingleSelect in the NocoDB UI.

```sql
-- Step 1: Drop the ENUM column and replace with text
ALTER TABLE your_table
  ALTER COLUMN status TYPE text;

-- Step 2: (Optional) Drop the ENUM type if nothing else uses it
DROP TYPE IF EXISTS status_enum;
```

After the migration, open NocoDB, go to the column settings for `status`, and change the field type to **SingleSelect**. Add the allowed values (draft, active, archived) there. NocoDB enforces the value set through its UI layer, not at the database level.

---

## 2. ARRAY Columns Are Not Supported

**Problem:** PostgreSQL `ARRAY[]` types (e.g., `VARCHAR(255)[]`, `TEXT[]`, `INTEGER[]`) are not supported by NocoDB. Columns using any array type will not be properly read.

**Fix:** Replace ARRAY columns with `json`. Store the values as a JSON array.

```sql
-- Convert an array column to json
ALTER TABLE your_table
  ALTER COLUMN tags TYPE json USING array_to_json(tags);
```

After conversion, the data is stored as `["tag1", "tag2", "tag3"]` in a plain `json` column, which NocoDB can read and display.

**Important:** Use `json`, not `jsonb`. NocoDB handles `json` more reliably than `jsonb` across both NocoDB and NocoBase.

---

## 3. UUID Primary Keys Are Not Supported

**Problem:** NocoDB expects auto-incrementing integer primary keys (`serial` or `bigserial`). UUID primary keys (`uuid` type with `gen_random_uuid()` or `uuid_generate_v4()` defaults) break NocoDB's internal row identification and record creation logic.

**Fix:** Replace UUID primary keys with `serial` or `bigserial`. If you still need UUIDs for external API references, keep them as a separate non-PK column.

```sql
-- This requires recreating the table. Example migration:

-- 1. Add a new serial PK column
ALTER TABLE your_table ADD COLUMN new_id serial;

-- 2. Drop the old UUID primary key
ALTER TABLE your_table DROP CONSTRAINT your_table_pkey;

-- 3. Rename the old UUID column (keep it if needed for external references)
ALTER TABLE your_table RENAME COLUMN id TO uuid_ref;

-- 4. Rename the new column to id
ALTER TABLE your_table RENAME COLUMN new_id TO id;

-- 5. Set the new PK
ALTER TABLE your_table ADD PRIMARY KEY (id);

-- 6. Update any foreign keys in other tables to reference the new integer id
```

If other tables reference this table via FK using the old UUID column, you will need to update those FKs as well to point to the new integer `id`.

---

## Verification Checklist

After making these fixes, run through this checklist before reconnecting the database to NocoDB:

- [ ] PK: `serial` or `bigserial` with name `id`
- [ ] FK columns: type matches parent PK (`int4` for `serial`, `int8` for `bigserial`)
- [ ] FK constraints: `ON DELETE NO ACTION ON UPDATE NO ACTION`
- [ ] Index on every FK column
- [ ] One-to-One: `UNIQUE` on FK column
- [ ] Junction tables: composite PK from two FKs + FK constraints + indexes
- [ ] No `ARRAY[]`, `ENUM`, `POINT`, `POLYGON`, `INHERITS`
- [ ] JSON stored as `json` (not `jsonb`)
- [ ] Select/MultiSelect stored as `text`
- [ ] Names in `snake_case`, no reserved words

## Diagnostic Queries

Run these to verify your schema is clean:

```sql
-- Find tables without a serial/bigserial PK
SELECT t.table_name, c.column_name, c.data_type
FROM information_schema.tables t
JOIN information_schema.columns c ON t.table_name = c.table_name
WHERE t.table_schema = 'public'
  AND c.column_name = 'id'
  AND c.column_default NOT LIKE 'nextval%';

-- Find FK columns without indexes
SELECT tc.table_name, kcu.column_name
FROM information_schema.table_constraints tc
JOIN information_schema.key_column_usage kcu
  ON tc.constraint_name = kcu.constraint_name
WHERE tc.constraint_type = 'FOREIGN KEY'
  AND tc.table_schema = 'public'
  AND NOT EXISTS (
    SELECT 1 FROM pg_indexes
    WHERE tablename = tc.table_name
      AND indexdef LIKE '%' || kcu.column_name || '%'
  );
```

---

## Summary

| What you used | Why it fails | What to use instead |
|---|---|---|
| PostgreSQL `ENUM` type | NocoDB cannot map custom types | `text` + SingleSelect in NocoDB UI |
| `ARRAY[]` columns (e.g., `VARCHAR(255)[]`) | Not supported by NocoDB | `json` (store as JSON array) |
| `UUID` primary key | NocoDB expects auto-increment integer PK | `serial` / `bigserial` named `id` |
