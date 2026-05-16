# Primary Key Type for PostgreSQL + NocoBase External Data Source (Millions of Records)

## Recommendation: `bigserial`

For a database that will hold millions of records and is connected to NocoBase as an external data source, use **`bigserial`** (which is `int8` / `bigint` with auto-increment).

### Why `bigserial` over `serial`

| Type | Underlying | Max value | Enough for millions? |
|------|-----------|-----------|---------------------|
| `serial` | `int4` (32-bit) | ~2.1 billion | Yes, technically -- but leaves limited headroom if rows are frequently inserted and deleted, IDs are allocated in bulk, or the dataset grows beyond initial projections |
| `bigserial` | `int8` (64-bit) | ~9.2 quintillion | Effectively unlimited for any real-world scenario |

With millions of records and a long-lived production database, `bigserial` is the safe choice. The storage overhead is minimal (8 bytes vs 4 bytes per row), and it eliminates any risk of hitting the `int4` ceiling over the lifetime of the system.

### Compatibility with NocoBase

NocoBase, when connected as an **external data source**, reads the existing schema without modifying it. It correctly recognizes both `serial` and `bigserial` primary keys. There are no compatibility issues.

### Compatibility with NocoDB

NocoDB uses `serial` as its default for PostgreSQL tables, but it fully supports `bigserial`. If NocoDB is also managing the same database (as the primary schema manager), it will work with `bigserial` PKs without issues.

### What NOT to use

- **UUID** -- NocoDB expects auto-increment integer primary keys. UUID-based PKs will break NocoDB's relation handling.
- **Text-based PKs** -- same problem as UUID; NocoDB requires integer auto-increment.
- **Composite PKs** on regular tables -- composite PKs are reserved for many-to-many junction tables only.

## Table Template with `bigserial`

```sql
CREATE TABLE "public"."your_table" (
    "id"         bigserial    NOT NULL,
    "title"      text,
    "created_at" timestamp    DEFAULT now(),
    "updated_at" timestamp    DEFAULT now(),
    PRIMARY KEY ("id")
);
```

## Foreign Key Convention

When referencing a `bigserial` PK from another table, the FK column must use `int8` (not `int4`) to match the parent PK type:

```sql
CREATE TABLE "public"."orders" (
    "id"          bigserial    NOT NULL,
    "customer_id" int8         NOT NULL,
    "total"       decimal(12,2),
    "created_at"  timestamp    DEFAULT now(),
    "updated_at"  timestamp    DEFAULT now(),
    PRIMARY KEY ("id"),
    CONSTRAINT "fk_orders_customer" FOREIGN KEY ("customer_id")
        REFERENCES "public"."customers" ("id") ON DELETE NO ACTION ON UPDATE NO ACTION
);

CREATE INDEX "idx_orders_customer_id" ON "public"."orders" ("customer_id");
```

Key points for FK columns:

- Use `int8` to match the `bigserial` parent PK (using `int4` would cause a type mismatch error).
- Add a `CONSTRAINT` with explicit `ON DELETE NO ACTION ON UPDATE NO ACTION` (NocoDB convention).
- Always create an `INDEX` on every FK column for query performance.

## Many-to-Many Junction Table Example

Junction tables use a **composite primary key** (no separate `id` column), which is the NocoDB convention:

```sql
CREATE TABLE "public"."order_products" (
    "order_id"   int8 NOT NULL,
    "product_id" int8 NOT NULL,
    PRIMARY KEY ("order_id", "product_id"),
    CONSTRAINT "fk_order_products_order" FOREIGN KEY ("order_id")
        REFERENCES "public"."orders" ("id") ON DELETE NO ACTION ON UPDATE NO ACTION,
    CONSTRAINT "fk_order_products_product" FOREIGN KEY ("product_id")
        REFERENCES "public"."products" ("id") ON DELETE NO ACTION ON UPDATE NO ACTION
);

CREATE INDEX "idx_order_products_order_id" ON "public"."order_products" ("order_id");
CREATE INDEX "idx_order_products_product_id" ON "public"."order_products" ("product_id");
```

## Summary

| Decision | Choice | Reason |
|----------|--------|--------|
| PK type | `bigserial` | Millions of records need headroom; 64-bit eliminates overflow risk |
| PK column name | `id` | Convention for both NocoDB and NocoBase |
| FK column type | `int8` | Must match parent `bigserial` PK type |
| UUID/text PK | Never | NocoDB requires auto-increment integers |
| NocoBase compatibility | Confirmed | Reads `bigserial` as external data source without issues |
| NocoDB compatibility | Confirmed | Supports `bigserial` despite defaulting to `serial` |
