# Product Catalog Column Types for PostgreSQL + NocoDB

Here are the recommended SQL types for each column in your product catalog, chosen for full compatibility between PostgreSQL and NocoDB:

| Column | SQL Type | NocoDB UIType | Why This Type |
|--------|----------|---------------|---------------|
| Product name | `text` | `SingleLineText` | NocoDB default for text in PG. Works as both SingleLineText and LongText depending on NocoDB UI config. |
| Price | `decimal(10, 2)` | `Currency` | Use `decimal` with explicit precision/scale for money. Avoids floating-point rounding. NocoDB recognizes it as Decimal, Currency, or Duration. |
| Discount percentage | `double precision` | `Percent` | NocoDB maps `double precision` to its Percent UIType. This is the verified type for percentages. |
| Is active | `bool` | `Checkbox` | Maps directly to NocoDB Checkbox. |
| Rating (1-5 stars) | `smallint` | `Rating` | NocoDB default for Rating in PG is `smallint` / `int2`. Displays as a star rating widget. |
| JSON metadata | `json` | `JSON` | Use `json` (not `jsonb`). Both work, but `json` is the safer choice for NocoDB compatibility. |

## SQL to Add These Columns

```sql
-- Product name
ALTER TABLE "public"."products" ADD "name" text;

-- Price (up to 99,999,999.99)
ALTER TABLE "public"."products" ADD "price" decimal(10, 2);

-- Discount percentage
ALTER TABLE "public"."products" ADD "discount" double precision;

-- Whether the product is active
ALTER TABLE "public"."products" ADD "is_active" bool DEFAULT false;

-- Star rating 1-5
ALTER TABLE "public"."products" ADD "rating" smallint DEFAULT 0;

-- Arbitrary JSON metadata
ALTER TABLE "public"."products" ADD "metadata" json;
```

## Key Notes

- **Price**: Always use `decimal(precision, scale)` for currency, never `double precision` or `float`. The `decimal` type avoids floating-point rounding errors (e.g., 19.99 staying as 19.99, not 19.990000000000002).
- **Discount**: `double precision` is the correct type here because NocoDB maps it to the Percent UIType, which renders with a `%` symbol in the NocoDB interface.
- **Rating**: `smallint` is what NocoDB creates by default for Rating columns in PostgreSQL. You can configure the max stars (1-5, 1-10, etc.) in the NocoDB UI; the SQL column just stores the integer value.
- **JSON**: Prefer `json` over `jsonb`. While `jsonb` also maps to NocoDB's JSON UIType and supports PostgreSQL indexing, `json` is the safer and more predictable choice for NocoDB compatibility.
- **is_active**: Setting `DEFAULT false` ensures new products start as inactive, which NocoDB renders as an unchecked checkbox.
