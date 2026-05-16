# E-Commerce Schema for PostgreSQL + NocoDB + NocoBase

A complete online store schema with 8 tables covering users, products, orders, and categories -- plus profiles, tags, and order items. Every type and constraint is chosen for compatibility with NocoDB (primary system, manages schema) and NocoBase (external data source, reads schema only).

## Platform Roles

| Platform | Role | What it does |
|----------|------|--------------|
| NocoDB | Primary system | Creates/modifies tables, manages FK constraints |
| NocoBase | External data source | Reads schema only, does not modify structure |

Both systems correctly read `serial` PKs, physical FK constraints, composite PKs on junction tables, `timestamp` columns, and `json` fields.

---

## Tables

### users

```sql
CREATE TABLE "public"."users" (
    "id"         serial             NOT NULL,
    "username"   text,
    "email"      character varying  UNIQUE,
    "created_at" timestamp          DEFAULT now(),
    "updated_at" timestamp          DEFAULT now(),
    PRIMARY KEY ("id")
);
```

### profiles (One-to-One with users)

```sql
CREATE TABLE "public"."profiles" (
    "id"         serial    NOT NULL,
    "user_id"    int4      UNIQUE,
    "first_name" text,
    "last_name"  text,
    "bio"        text,
    "birth_date" date,
    "created_at" timestamp DEFAULT now(),
    "updated_at" timestamp DEFAULT now(),
    PRIMARY KEY ("id")
);
```

`UNIQUE` on `user_id` is what makes NocoDB identify this as a One-to-One relation rather than One-to-Many.

### categories (Self-referential tree)

```sql
CREATE TABLE "public"."categories" (
    "id"         serial    NOT NULL,
    "title"      text      NOT NULL,
    "parent_id"  int4,
    "created_at" timestamp DEFAULT now(),
    "updated_at" timestamp DEFAULT now(),
    PRIMARY KEY ("id")
);
```

`parent_id` references the same table, enabling nested category hierarchies (e.g., Electronics > Phones > Smartphones).

### products

```sql
CREATE TABLE "public"."products" (
    "id"          serial        NOT NULL,
    "title"       text          NOT NULL,
    "description" text,
    "price"       decimal(10, 2),
    "quantity"    int4          DEFAULT 0,
    "is_active"   bool          DEFAULT true,
    "rating"      smallint      DEFAULT 0,
    "metadata"    json,
    "category_id" int4,
    "created_at"  timestamp     DEFAULT now(),
    "updated_at"  timestamp     DEFAULT now(),
    PRIMARY KEY ("id")
);
```

Column type rationale:
- `decimal(10, 2)` for price -- exact currency math, no floating-point rounding
- `smallint` for rating -- NocoDB maps this to its Rating UI type
- `json` (not `jsonb`) -- both platforms read it correctly; `jsonb` works but `json` is safer
- `bool` for is_active -- NocoDB shows as Checkbox, NocoBase as boolean toggle
- `text` for description -- NocoDB maps to LongText UI type

### tags

```sql
CREATE TABLE "public"."tags" (
    "id"         serial    NOT NULL,
    "name"       text      NOT NULL UNIQUE,
    "created_at" timestamp DEFAULT now(),
    "updated_at" timestamp DEFAULT now(),
    PRIMARY KEY ("id")
);
```

### nc_m2m_products_tags (Many-to-Many junction)

```sql
CREATE TABLE "public"."nc_m2m_products_tags" (
    "product_id" int4 NOT NULL,
    "tag_id"     int4 NOT NULL,
    PRIMARY KEY ("product_id", "tag_id")
);
```

NocoDB convention for junction tables: composite PK from both FK columns, no separate `id` column. The `nc_m2m_` prefix is the NocoDB naming pattern. NocoBase reads composite PK tables correctly as an external source.

### orders

```sql
CREATE TABLE "public"."orders" (
    "id"         serial        NOT NULL,
    "user_id"    int4,
    "status"     text          DEFAULT 'pending',
    "total"      decimal(12, 2),
    "notes"      text,
    "ordered_at" timestamp     DEFAULT now(),
    "created_at" timestamp     DEFAULT now(),
    "updated_at" timestamp     DEFAULT now(),
    PRIMARY KEY ("id")
);
```

`status` uses `text` (not `enum`) -- NocoDB uses `text` for SingleSelect fields in PostgreSQL, and this is compatible with NocoBase. Status values (pending, paid, shipped, delivered, cancelled) are configured in NocoDB's UI, not in SQL.

### order_items

```sql
CREATE TABLE "public"."order_items" (
    "id"         serial         NOT NULL,
    "order_id"   int4           NOT NULL,
    "product_id" int4           NOT NULL,
    "quantity"   int4           NOT NULL DEFAULT 1,
    "unit_price" decimal(10, 2) NOT NULL,
    "created_at" timestamp      DEFAULT now(),
    "updated_at" timestamp      DEFAULT now(),
    PRIMARY KEY ("id")
);
```

---

## FK Constraints

All constraints use `ON DELETE NO ACTION ON UPDATE NO ACTION` -- the NocoDB default. Deleting a parent row is blocked if child records exist. Both platforms correctly read and respect this behavior.

```sql
ALTER TABLE "public"."profiles"
    ADD CONSTRAINT "fk_users_profiles"
    FOREIGN KEY ("user_id") REFERENCES "public"."users" ("id")
    ON DELETE NO ACTION ON UPDATE NO ACTION;

ALTER TABLE "public"."categories"
    ADD CONSTRAINT "fk_categories_parent"
    FOREIGN KEY ("parent_id") REFERENCES "public"."categories" ("id")
    ON DELETE NO ACTION ON UPDATE NO ACTION;

ALTER TABLE "public"."products"
    ADD CONSTRAINT "fk_categories_products"
    FOREIGN KEY ("category_id") REFERENCES "public"."categories" ("id")
    ON DELETE NO ACTION ON UPDATE NO ACTION;

ALTER TABLE "public"."nc_m2m_products_tags"
    ADD CONSTRAINT "fk_products_tags_1"
    FOREIGN KEY ("product_id") REFERENCES "public"."products" ("id")
    ON DELETE NO ACTION ON UPDATE NO ACTION;

ALTER TABLE "public"."nc_m2m_products_tags"
    ADD CONSTRAINT "fk_products_tags_2"
    FOREIGN KEY ("tag_id") REFERENCES "public"."tags" ("id")
    ON DELETE NO ACTION ON UPDATE NO ACTION;

ALTER TABLE "public"."orders"
    ADD CONSTRAINT "fk_users_orders"
    FOREIGN KEY ("user_id") REFERENCES "public"."users" ("id")
    ON DELETE NO ACTION ON UPDATE NO ACTION;

ALTER TABLE "public"."order_items"
    ADD CONSTRAINT "fk_orders_items"
    FOREIGN KEY ("order_id") REFERENCES "public"."orders" ("id")
    ON DELETE NO ACTION ON UPDATE NO ACTION;

ALTER TABLE "public"."order_items"
    ADD CONSTRAINT "fk_products_items"
    FOREIGN KEY ("product_id") REFERENCES "public"."products" ("id")
    ON DELETE NO ACTION ON UPDATE NO ACTION;
```

---

## Indexes

Every FK column gets an index. This is required for query performance and follows NocoDB conventions.

```sql
CREATE INDEX "idx_profiles_user_id"       ON "public"."profiles" ("user_id");
CREATE INDEX "idx_categories_parent_id"   ON "public"."categories" ("parent_id");
CREATE INDEX "idx_products_category_id"   ON "public"."products" ("category_id");
CREATE INDEX "idx_m2m_product_id"         ON "public"."nc_m2m_products_tags" ("product_id");
CREATE INDEX "idx_m2m_tag_id"             ON "public"."nc_m2m_products_tags" ("tag_id");
CREATE INDEX "idx_orders_user_id"         ON "public"."orders" ("user_id");
CREATE INDEX "idx_order_items_order_id"   ON "public"."order_items" ("order_id");
CREATE INDEX "idx_order_items_product_id" ON "public"."order_items" ("product_id");
```

---

## Relation Map

```
users     --> profiles       : One-to-One   (UNIQUE FK on user_id)
users     --> orders         : One-to-Many
categories --> categories    : Self-referential tree (parent_id)
categories --> products      : One-to-Many
products  <-> tags           : Many-to-Many (junction: nc_m2m_products_tags)
orders    --> order_items    : One-to-Many
products  --> order_items    : One-to-Many
```

This schema demonstrates all four relation types supported by both platforms:

| Relation Type | Example | How it works |
|---------------|---------|--------------|
| **One-to-One** | users --> profiles | `UNIQUE` constraint on FK column (`user_id`) |
| **One-to-Many** | users --> orders | Standard FK without UNIQUE |
| **Many-to-Many** | products <-> tags | Junction table with composite PK, no separate `id` |
| **Self-referential** | categories --> categories | FK column (`parent_id`) references same table's PK |

---

## Execution Order

Run the SQL in this order to avoid FK reference errors:

1. **Tables** -- `users`, `profiles`, `categories`, `products`, `tags`, `nc_m2m_products_tags`, `orders`, `order_items`
2. **FK constraints** -- all `ALTER TABLE ... ADD CONSTRAINT` statements
3. **Indexes** -- all `CREATE INDEX` statements

---

## Column Type Summary

| Column | SQL Type | NocoDB UI | NocoBase Field | Why this type |
|--------|----------|-----------|----------------|---------------|
| id | `serial` | AutoNumber | integer | NocoDB PG default; NocoBase reads it |
| username, title, name | `text` | SingleLineText | string | NocoDB PG default for text |
| email | `character varying` | Email | string | NocoDB default for Email |
| description, bio, notes | `text` | LongText | text | Same SQL type, UI differs |
| price, total, unit_price | `decimal(p,s)` | Currency/Decimal | decimal | Exact math, no float rounding |
| quantity | `int4` | Number | integer | Whole numbers |
| rating | `smallint` | Rating | integer | NocoDB maps smallint to Rating |
| is_active | `bool` | Checkbox | boolean | Standard boolean |
| status | `text` | SingleSelect | string | NocoDB uses text for selects in PG |
| metadata | `json` | JSON | json | Both systems handle `json` |
| birth_date | `date` | Date | dateOnly | Date without time |
| created_at, updated_at | `timestamp` | DateTime | datetimeNoTz | NocoDB default, no timezone |
| FK columns (user_id, etc.) | `int4` | ForeignKey | integer | Matches parent `serial` PK type |

---

## Forbidden Types

Do **not** use these in schemas intended for NocoDB + NocoBase external DB:

- `ARRAY[]` -- not supported by NocoDB
- `ENUM` -- NocoDB uses `text` for selects, not PG enums
- `POINT`, `POLYGON`, `PATH`, `CIRCLE` -- geometric types not compatible
- `INHERITS` -- table inheritance not supported
- `jsonb` -- works but `json` is the safer choice for cross-platform compatibility
