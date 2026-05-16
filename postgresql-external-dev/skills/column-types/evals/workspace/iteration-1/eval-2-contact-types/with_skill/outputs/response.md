# Contacts Table — PostgreSQL Types for NocoBase External Database

Here are the compatible PostgreSQL types for each field in your contacts table, verified to work across both NocoDB and NocoBase:

## Field-by-Field Recommendations

| Field | SQL Type | NocoDB UIType | NocoBase Field | Notes |
|-------|----------|---------------|----------------|-------|
| email | `character varying` | `Email` | `string` | NocoDB default for Email in PG. Provides built-in email validation in NocoDB UI. |
| phone | `character varying` | `PhoneNumber` | `string` | NocoDB default for PhoneNumber in PG. Displays with phone formatting in NocoDB. |
| website | `text` | `URL` | `string` / `text` | Renders as a clickable link in NocoDB. |
| status | `text` | `SingleSelect` | `string` | NocoDB uses `text` for SingleSelect in PostgreSQL. You configure the dropdown options (active/inactive/pending) in NocoDB's UI, not at the SQL level. |

## SQL to Create the Table

```sql
CREATE TABLE "public"."contacts" (
  "id" serial PRIMARY KEY,
  "email" character varying,
  "phone" character varying,
  "website" text,
  "status" text DEFAULT 'pending',
  "created_at" timestamp DEFAULT now(),
  "updated_at" timestamp DEFAULT now()
);
```

## Or, to Add Columns to an Existing Table

```sql
ALTER TABLE "public"."contacts" ADD "email" character varying;
ALTER TABLE "public"."contacts" ADD "phone" character varying;
ALTER TABLE "public"."contacts" ADD "website" text;
ALTER TABLE "public"."contacts" ADD "status" text DEFAULT 'pending';
```

## Key Points

**Email and Phone use `character varying`**, not `text`. This is the NocoDB default for these UI types in PostgreSQL. Both are fully compatible with NocoBase, which maps them to `string`.

**Website URL uses `text`**. NocoDB maps `text` to the `URL` UIType (configured in NocoDB, not at the SQL level). NocoBase reads it as `string` or `text`.

**Status (dropdown) uses `text`**. This is critical to understand: NocoDB stores SingleSelect values as plain `text` in PostgreSQL. There is no PostgreSQL enum involved. The dropdown options (active, inactive, pending) are configured in NocoDB's column settings, not in the SQL schema. NocoBase sees it as a regular `string` field. If you want to enforce allowed values at the database level, you can add a CHECK constraint, but this is optional:

```sql
ALTER TABLE "public"."contacts"
  ADD CONSTRAINT contacts_status_check
  CHECK (status IN ('active', 'inactive', 'pending'));
```

This constraint is purely for data integrity at the DB level -- NocoDB and NocoBase will still work without it. The dropdown behavior comes from NocoDB's UI configuration.
