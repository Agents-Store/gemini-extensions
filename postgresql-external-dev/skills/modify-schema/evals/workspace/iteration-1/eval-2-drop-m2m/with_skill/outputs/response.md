# Removing a Many-to-Many Relationship: `nc_m2m_products_tags`

When removing a junction table that implements a many-to-many relationship in PostgreSQL, you must drop the foreign key constraints **before** dropping the table itself. Attempting to drop the table while FK constraints still exist will either fail or require `CASCADE`, which can have unintended side effects on other objects.

## Safe Order of Operations

### Step 1 -- Drop both FK constraints on the junction table

The junction table `nc_m2m_products_tags` has two foreign key constraints: one referencing the `products` table and one referencing the `tags` table. Drop them both first:

```sql
-- Drop the FK constraint pointing to products
ALTER TABLE "public"."nc_m2m_products_tags" DROP CONSTRAINT "fk_products_tags_1";

-- Drop the FK constraint pointing to tags
ALTER TABLE "public"."nc_m2m_products_tags" DROP CONSTRAINT "fk_products_tags_2";
```

> **Note:** The actual constraint names in your database may differ. To find the real names, run:
> ```sql
> SELECT conname
> FROM pg_constraint
> WHERE conrelid = '"public"."nc_m2m_products_tags"'::regclass
>   AND contype = 'f';
> ```

### Step 2 -- Drop the junction table

Once both foreign key constraints are removed, drop the table:

```sql
DROP TABLE IF EXISTS "public"."nc_m2m_products_tags";
```

## Complete Script

```sql
-- 1. Drop FK constraints first
ALTER TABLE "public"."nc_m2m_products_tags" DROP CONSTRAINT "fk_products_tags_1";
ALTER TABLE "public"."nc_m2m_products_tags" DROP CONSTRAINT "fk_products_tags_2";

-- 2. Drop the junction table
DROP TABLE IF EXISTS "public"."nc_m2m_products_tags";
```

## Why This Order Matters

- **Constraints first, then table.** PostgreSQL will refuse to drop a table that is referenced by foreign keys from other tables (or that itself holds FK constraints pointing outward). By explicitly dropping the constraints first, you ensure a clean removal with no cascading surprises.
- **No CASCADE needed.** Dropping constraints explicitly avoids using `DROP TABLE ... CASCADE`, which could silently drop dependent views, triggers, or other constraints you did not intend to remove.
- If the `products` or `tags` tables have any indexes that were created specifically for this M2M relationship, you can drop those afterward with `DROP INDEX IF EXISTS`.
