# Pre-Connection Validation Checklist: PostgreSQL to NocoBase

NocoBase connects as a **read-only external data source** -- it reads your schema but never modifies it. That means every issue must be fixed in PostgreSQL before you connect; NocoBase will not correct anything for you.

Run through every section below before adding the external data source in NocoBase.

---

## 1. Primary Keys

- [ ] Every table has a `serial` or `bigserial` primary key column named `id`
- [ ] No UUID or text-based primary keys (NocoDB/NocoBase expect auto-increment integers)
- [ ] PK column has `NOT NULL` constraint

**Diagnostic query -- find tables without a serial/bigserial PK:**

```sql
SELECT t.table_name, c.column_name, c.data_type
FROM information_schema.tables t
JOIN information_schema.columns c ON t.table_name = c.table_name
WHERE t.table_schema = 'public'
  AND c.column_name = 'id'
  AND c.column_default NOT LIKE 'nextval%';
```

If any rows come back, those tables need their PK changed to `serial` or `bigserial`.

---

## 2. Foreign Keys and Indexes

- [ ] Every FK column type matches the parent PK type (`int4` for `serial`, `int8` for `bigserial`)
- [ ] Every FK has an explicit `CONSTRAINT` defined (`ALTER TABLE ... ADD CONSTRAINT ... FOREIGN KEY ...`)
- [ ] All FK constraints use `ON DELETE NO ACTION ON UPDATE NO ACTION`
- [ ] Every FK column has a `CREATE INDEX` on it
- [ ] One-to-One relations have a `UNIQUE` constraint on the FK column

**Diagnostic query -- find FK columns missing indexes:**

```sql
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

## 3. Junction Tables (Many-to-Many)

- [ ] Junction tables use a **composite primary key** from both FK columns (no separate `id` column)
- [ ] Both FK columns have explicit FK constraints
- [ ] Both FK columns have indexes

---

## 4. Forbidden Types

These PostgreSQL types are not supported. Replace them before connecting.

- [ ] No `ARRAY[]` types (e.g. `VARCHAR(255)[]`) -- replace with `json`
- [ ] No `ENUM` types -- replace with `text` (use SingleSelect in the UI)
- [ ] No geometric types (`POINT`, `PATH`, `POLYGON`, `CIRCLE`) -- replace with `json`
- [ ] No `INHERITS` (table inheritance) -- replace with regular tables + FK
- [ ] No `jsonb` columns -- replace with `json` (safer for both NocoDB and NocoBase)

---

## 5. Column Types Compatibility

- [ ] JSON data stored as `json` (not `jsonb`)
- [ ] Select/MultiSelect fields stored as `text`
- [ ] Timestamps use `timestamp` (NocoDB default) or `timestamptz` -- both work
- [ ] Decimal values use `decimal(precision, scale)`, e.g. `decimal(10, 2)`
- [ ] Rating fields use `smallint`
- [ ] Boolean fields use `bool`

---

## 6. Naming Conventions

- [ ] All table and column names use `snake_case`
- [ ] No spaces in column names
- [ ] No PostgreSQL reserved words as column names (`order`, `user`, `group`, `table`, etc.) -- add a suffix: `sort_order`, `app_user`, `user_group`

---

## 7. Defaults and Constraints

- [ ] No `DEFAULT` set on `json` columns (set defaults in the application instead)
- [ ] `created_at` and `updated_at` columns use `DEFAULT now()`
- [ ] No `ON DELETE CASCADE` on FK constraints (use `ON DELETE NO ACTION`)

---

## Quick Summary Table

| Check | Rule |
|-------|------|
| PK | `serial` or `bigserial` named `id` |
| FK type | Must match parent PK (`int4` / `int8`) |
| FK constraint | `ON DELETE NO ACTION ON UPDATE NO ACTION` |
| FK index | `CREATE INDEX` on every FK column |
| One-to-One | `UNIQUE` on FK column |
| Junction table | Composite PK, no separate `id` |
| Forbidden types | No `ARRAY`, `ENUM`, `POINT`, `POLYGON`, `INHERITS`, `jsonb` |
| JSON | Use `json`, not `jsonb` |
| Select fields | Use `text` |
| Naming | `snake_case`, no reserved words |
| JSON defaults | None -- set in app |
| Delete behavior | `NO ACTION`, never `CASCADE` |

---

If all boxes are checked, your PostgreSQL database is ready to be added as an external data source in NocoBase. NocoBase will read the schema, detect your tables, columns, and FK relations, and map them to its internal field types automatically.
