# Many-to-Many Relation: articles <-> categories

Since both `articles` and `categories` tables already exist with `serial` PKs, you only need the junction table, FK constraints, and indexes.

## Complete SQL

```sql
-- 1. Junction table (NocoDB style — composite PK, no separate id)
CREATE TABLE "public"."nc_m2m_articles_categories" (
    "articles_id"   int4 NOT NULL,
    "categories_id" int4 NOT NULL,
    PRIMARY KEY ("articles_id", "categories_id")
);

-- 2. FK constraints (NocoDB style)
ALTER TABLE "public"."nc_m2m_articles_categories"
    ADD CONSTRAINT "fk_articles_categories_1"
    FOREIGN KEY ("articles_id")
    REFERENCES "public"."articles" ("id")
    ON DELETE NO ACTION
    ON UPDATE NO ACTION;

ALTER TABLE "public"."nc_m2m_articles_categories"
    ADD CONSTRAINT "fk_articles_categories_2"
    FOREIGN KEY ("categories_id")
    REFERENCES "public"."categories" ("id")
    ON DELETE NO ACTION
    ON UPDATE NO ACTION;

-- 3. Indexes on both FK columns
CREATE INDEX "idx_m2m_articles_id"   ON "public"."nc_m2m_articles_categories" ("articles_id");
CREATE INDEX "idx_m2m_categories_id" ON "public"."nc_m2m_articles_categories" ("categories_id");
```

## What this gives you

| Component | Purpose |
|-----------|---------|
| `nc_m2m_articles_categories` | Junction table linking the two tables |
| Composite PK (`articles_id`, `categories_id`) | Guarantees each article-category pair is unique -- no duplicate associations |
| `fk_articles_categories_1` | FK constraint ensuring `articles_id` references a valid row in `articles` |
| `fk_articles_categories_2` | FK constraint ensuring `categories_id` references a valid row in `categories` |
| `idx_m2m_articles_id` | Index for fast lookups by article (e.g., "find all categories for article X") |
| `idx_m2m_categories_id` | Index for fast lookups by category (e.g., "find all articles in category Y") |

## Key design decisions

- **`nc_m2m_` prefix**: NocoDB naming convention for junction tables. NocoDB will auto-detect this as a many-to-many relation.
- **No separate `id` column**: The composite PK from both FK columns is sufficient. NocoDB expects this pattern.
- **`int4` column type**: Matches the `serial` PK type on both parent tables. If your PKs were `bigserial`, you would use `int8` instead.
- **`ON DELETE NO ACTION`**: NocoDB default. Deleting an article or category is blocked if junction rows reference it. To allow cascade deletion, change to `ON DELETE CASCADE`.
- **NocoBase compatibility**: NocoBase as an external data source correctly reads tables with composite PKs and FK constraints.

## Verification checklist

- [x] FK column types match parent PK types (`int4` for `serial`)
- [x] FK constraints use `ON DELETE NO ACTION ON UPDATE NO ACTION`
- [x] Index created on every FK column
- [x] Junction table uses composite PK from both FK columns
- [x] Junction table has FK constraints on both columns
- [x] Junction table has indexes on both FK columns
