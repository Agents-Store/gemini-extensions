# Modify `products` Table -- Rename, Change Type, Add Column

Here are the three ALTER TABLE statements for your `products` table, following the safe modification order (modify existing columns first, then add new ones).

## 1. Rename `name` to `title`

```sql
ALTER TABLE "public"."products" RENAME COLUMN "name" TO "title";
```

## 2. Change `price` from int4 to decimal(10,2)

Since you are changing the column type, drop any existing default first, then cast the data:

```sql
ALTER TABLE "public"."products" ALTER COLUMN "price" DROP DEFAULT;
ALTER TABLE "public"."products" ALTER COLUMN "price" TYPE decimal(10, 2)
    USING "price"::decimal(10, 2);
```

The `USING` clause ensures existing integer values are safely cast to the new decimal type.

## 3. Add `description` text column

```sql
ALTER TABLE "public"."products" ADD "description" text;
```

## Complete Script

Run all statements together in this order:

```sql
-- 1. Rename column
ALTER TABLE "public"."products" RENAME COLUMN "name" TO "title";

-- 2. Change column type (drop default first, then cast)
ALTER TABLE "public"."products" ALTER COLUMN "price" DROP DEFAULT;
ALTER TABLE "public"."products" ALTER COLUMN "price" TYPE decimal(10, 2)
    USING "price"::decimal(10, 2);

-- 3. Add new column
ALTER TABLE "public"."products" ADD "description" text;
```

After running these statements, sync the external data source in NocoDB so it picks up the schema changes.
