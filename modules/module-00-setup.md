[← Narrative Arc](../03-narrative-arc.md)  |  [🏠 Home](../README.md)  |  [Module 01: Unity Catalog →](module-01-unity-catalog.md)

---

## MODULE 00: Setup

**METADATA**
- Time estimate: 15 minutes
- Feature(s) covered: Account/role bootstrap
- Depends on: Nothing (first module)
- Key artifact created: RAD_CATALOG with RAD_FOUNDATION and RAD_AGENTS schemas, roles, and warehouse

### Documentation references

Use these Databricks docs for the setup steps in this module:

* [Manage users, service principals, and groups](https://docs.databricks.com/aws/en/admin/users-groups/index/)
* [Manage groups](https://docs.databricks.com/aws/en/admin/users-groups/manage-groups/)
* [Identity best practices](https://docs.databricks.com/aws/en/admin/users-groups/best-practices/)
* [Unity Catalog setup guide](https://docs.databricks.com/aws/en/data-governance/unity-catalog/setup-uc/)
* [Unity Catalog permissions model concepts](https://docs.databricks.com/aws/en/data-governance/unity-catalog/access-control/permissions-concepts/)
* [SQL warehouses overview](https://docs.databricks.com/aws/en/compute/sql-warehouse/index/)
* [Set up serverless SQL warehouses](https://docs.databricks.com/aws/en/admin/sql/serverless/)

**Instructor note:** Steps 1–4 below can be pre-run by the instructor to save ~10 minutes of lab time, if running as a live session with a group.

**Steps:**

1. Create the catalog and schemas:
```sql
CREATE CATALOG IF NOT EXISTS RAD_CATALOG;
CREATE SCHEMA IF NOT EXISTS RAD_CATALOG.RAD_FOUNDATION;
CREATE SCHEMA IF NOT EXISTS RAD_CATALOG.RAD_AGENTS;
```

2. Create the SQL warehouse (or confirm one exists — Genie Agents require a pro or serverless SQL warehouse):
```sql
-- [XXX] -- [XXX]: Use an existing pro/serverless warehouse in your workspace,
-- or create RAD_WH via the Databricks UI (SQL Warehouses > Create).
-- This lab does not script warehouse creation via SQL — it is a workspace-level object.
```
UI ALTERNATIVE: Go to SQL Warehouses in the left nav > Create SQL Warehouse > name it `RAD_WH` > size XS or 2X-Small > enable auto-stop at 10 minutes.

3. Create account-level groups, assign them to the workspace, then grant access.

UI walkthrough for this step:
1. If you want a separate business-user persona, create a second user first:
   * In the account console, go to User management > Users.
   * Click Add user.
   * Enter the user's name and email address.
   * Save, then wait for the invitation email to be accepted.
   * If you prefer a workspace-first flow, a workspace admin can also go to the workspace Settings > Identity and access > Users > Add user, which both creates the user and assigns them to that workspace.
2. In the account console, go to User management > Groups.
3. Create `RAD_ADMIN`, `RAD_ANALYST`, and `RAD_BUSINESS_USER`.
4. Add members to each group:
   * `RAD_ADMIN`: add the person running Module 00 setup. In a self-paced lab, this is usually you.
   * `RAD_ANALYST`: add the person who will build and tune the Genie Agent. In a self-paced lab, this can also be you.
   * `RAD_BUSINESS_USER`: ideally add a second test user who will only use Genie and not perform setup.
   * If you only have one user, you may add yourself to all three groups to complete the lab mechanics, but note that this does not demonstrate true persona separation.
5. Assign the groups to the lab workspace:
   * In the account console, go to Workspaces.
   * Open your target workspace.
   * On the Permissions tab, click Add permissions.
   * Search for the group, select it, and apply workspace entitlements as follows:
      * `RAD_ADMIN`: turn on Databricks SQL access so this persona can run the setup and grant statements. Turn on Workspace access only if this same persona will also use notebooks or other workspace assets during the lab. Consumer access is optional. Leave Admin access off unless you intentionally want the group itself to have workspace-admin powers; for most labs, rely on the participant's existing user-level admin role instead.
      * `RAD_ANALYST`: turn on Databricks SQL access and Workspace access. This persona needs SQL access to create and curate the Genie Agent and Workspace access for Module 03 notebook validation. Consumer access is optional. Leave Admin access off.
      * `RAD_BUSINESS_USER`: turn on Consumer access only. This persona is meant to use the shared Genie Agent experience without SQL authoring or notebook access. Leave Databricks SQL access, Workspace access, and Admin access off unless you intentionally want a broader demo than the lab's least-privilege story.
   * Save each assignment.
   * At minimum, assign the three lab groups to the workspace before running the GRANT statements.
6. If you created a second test user, make sure that user can access the workspace either through direct user assignment or through one of the assigned lab groups.
7. Return to the SQL query and run the GRANT statements.

Instructor note: do not require every participant to create a second user. For most self-paced or instructor-led labs, a single login is sufficient to learn the mechanics. Treat the second user as an optional enhancement when you specifically want to demonstrate business-user behavior under a different identity.

```sql
-- Do NOT use CREATE GROUP here for Unity Catalog access.
-- In Databricks SQL, CREATE GROUP creates a workspace-local group, which Unity Catalog cannot grant to.
-- Instead, create these as account-level groups in the account console or via SCIM first:
--   RAD_ADMIN
--   RAD_ANALYST
--   RAD_BUSINESS_USER

GRANT ALL PRIVILEGES ON CATALOG RAD_CATALOG TO `RAD_ADMIN`;
GRANT ALL PRIVILEGES ON SCHEMA RAD_CATALOG.RAD_FOUNDATION TO `RAD_ADMIN`;
GRANT ALL PRIVILEGES ON SCHEMA RAD_CATALOG.RAD_AGENTS TO `RAD_ADMIN`;

GRANT USE CATALOG ON CATALOG RAD_CATALOG TO `RAD_ANALYST`;
GRANT USE SCHEMA ON SCHEMA RAD_CATALOG.RAD_FOUNDATION TO `RAD_ANALYST`;
GRANT USE SCHEMA ON SCHEMA RAD_CATALOG.RAD_AGENTS TO `RAD_ANALYST`;

GRANT USE CATALOG ON CATALOG RAD_CATALOG TO `RAD_BUSINESS_USER`;
GRANT USE SCHEMA ON SCHEMA RAD_CATALOG.RAD_FOUNDATION TO `RAD_BUSINESS_USER`;
GRANT USE SCHEMA ON SCHEMA RAD_CATALOG.RAD_AGENTS TO `RAD_BUSINESS_USER`;

-- Give the analyst explicit read access to all foundation tables used to build
-- and tune the Genie Agent. This keeps the persona story clear in later modules.
GRANT SELECT ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE TO `RAD_ANALYST`;
GRANT SELECT ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_SALES_TXN TO `RAD_ANALYST`;
GRANT SELECT ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS TO `RAD_ANALYST`;
GRANT SELECT ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_SKU_LOOKUP TO `RAD_ANALYST`;
GRANT SELECT ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_PRODUCT_REVIEWS TO `RAD_ANALYST`;
```
If a GRANT fails with a principal-not-found error, confirm the groups were created at the account level and assigned to this workspace before continuing.

4. Create the five tables and load the sample rows from Section 2b.
```sql
CREATE OR REPLACE TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE (
  store_id STRING,
  store_name STRING,
  region STRING,
  state STRING,
  sq_footage INT,
  open_date DATE
);

INSERT INTO RAD_CATALOG.RAD_FOUNDATION.RAD_STORE VALUES
  ('RAD-1001', 'Radiance Buckhead', 'Southeast', 'GA', 4200, DATE '2018-03-14'),
  ('RAD-1002', 'Radiance SoHo', 'Northeast', 'NY', 6800, DATE '2015-09-01'),
  ('RAD-1003', 'Radiance Naperville', 'Midwest', 'IL', 3900, DATE '2020-11-20'),
  ('RAD-1004', 'Radiance Scottsdale', 'West', 'AZ', 5100, DATE '2019-06-05'),
  ('RAD-1005', 'Radiance Dilworth', 'Southeast', 'NC', 3600, DATE '2021-02-17');

CREATE OR REPLACE TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_SALES_TXN (
  txn_id STRING,
  store_id STRING,
  sku_id STRING,
  sale_date DATE,
  quantity INT,
  sale_amount DECIMAL(10,2),
  channel STRING
);

INSERT INTO RAD_CATALOG.RAD_FOUNDATION.RAD_SALES_TXN VALUES
  ('TXN-9001', 'RAD-1001', 'SKU-441', DATE '2026-07-01', 1, 42.00, 'in_store'),
  ('TXN-9002', 'RAD-1002', 'SKU-119', DATE '2026-07-01', 2, 76.50, 'in_store'),
  ('TXN-9003', 'RAD-1001', 'SKU-220', DATE '2026-07-02', 1, 28.00, 'online_pickup'),
  ('TXN-9004', 'RAD-1004', 'SKU-441', DATE '2026-07-02', 3, 126.00, 'in_store'),
  ('TXN-9005', 'RAD-1003', 'SKU-119', DATE '2026-07-03', 1, 38.25, 'in_store');

CREATE OR REPLACE TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS (
  store_id STRING,
  metric_date DATE,
  daily_sales DECIMAL(10,2),
  conversion_rate DECIMAL(5,4),
  foot_traffic INT,
  avg_basket_size DECIMAL(10,2)
);

INSERT INTO RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS VALUES
  ('RAD-1001', DATE '2026-07-01', 3120.00, 0.2810, 340, 40.60),
  ('RAD-1002', DATE '2026-07-01', 5980.50, 0.3120, 512, 44.15),
  ('RAD-1003', DATE '2026-07-01', 1890.00, 0.2205, 275, 31.20),
  ('RAD-1004', DATE '2026-07-01', 4410.00, 0.2990, 398, 38.90),
  ('RAD-1005', DATE '2026-07-01', 2205.75, 0.2450, 290, 33.10);

CREATE OR REPLACE TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_PRODUCT_REVIEWS (
  review_id STRING,
  sku_id STRING,
  review_text STRING,
  sentiment_score DECIMAL(3,2),
  review_date DATE
);

INSERT INTO RAD_CATALOG.RAD_FOUNDATION.RAD_PRODUCT_REVIEWS VALUES
  ('REV-501', 'SKU-441', 'Best moisturizer I''ve used, absorbs fast', 0.92, DATE '2026-06-28'),
  ('REV-502', 'SKU-119', 'Good but the pump breaks after a month', 0.15, DATE '2026-06-29'),
  ('REV-503', 'SKU-220', 'Love the scent, will repurchase', 0.85, DATE '2026-06-30'),
  ('REV-504', 'SKU-441', 'Too greasy for my skin type', -0.40, DATE '2026-07-01'),
  ('REV-505', 'SKU-119', 'Perfect for sensitive skin, no irritation at all', 0.88, DATE '2026-07-01');

CREATE OR REPLACE TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_SKU_LOOKUP (
  sku_id STRING,
  product_name STRING,
  category STRING,
  brand STRING,
  price DECIMAL(10,2)
);

INSERT INTO RAD_CATALOG.RAD_FOUNDATION.RAD_SKU_LOOKUP VALUES
  ('SKU-441', 'Hydra Glow Moisturizer', 'skincare', 'Lumine', 42.00),
  ('SKU-119', 'Silk Repair Serum', 'haircare', 'Verdant', 38.25),
  ('SKU-220', 'Matte Finish Foundation', 'cosmetics', 'Radiance Co', 28.00),
  ('SKU-330', 'Overnight Renewal Mask', 'skincare', 'Lumine', 34.00),
  ('SKU-410', 'Volumizing Mousse', 'haircare', 'Verdant', 22.50);
```
Using `CREATE OR REPLACE TABLE` keeps Module 00 rerunnable without duplicating sample data. If you rerun this step after completing Module 01, reapply any table-level comments or grants from later modules as needed.

**VALIDATION CHECKPOINT**
```sql
-- VALIDATION: Module 00 — confirms all 5 tables exist with data
SELECT 'RAD_STORE' AS table_name, COUNT(*) AS row_check FROM RAD_CATALOG.RAD_FOUNDATION.RAD_STORE
UNION ALL
SELECT 'RAD_SALES_TXN' AS table_name, COUNT(*) AS row_check FROM RAD_CATALOG.RAD_FOUNDATION.RAD_SALES_TXN
UNION ALL
SELECT 'RAD_STORE_METRICS' AS table_name, COUNT(*) AS row_check FROM RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS
UNION ALL
SELECT 'RAD_PRODUCT_REVIEWS' AS table_name, COUNT(*) AS row_check FROM RAD_CATALOG.RAD_FOUNDATION.RAD_PRODUCT_REVIEWS
UNION ALL
SELECT 'RAD_SKU_LOOKUP' AS table_name, COUNT(*) AS row_check FROM RAD_CATALOG.RAD_FOUNDATION.RAD_SKU_LOOKUP;
-- Expected: 5 rows returned, one per table, each with row_check = 5
-- If any table shows 0 rows, the INSERT statements did not run for that table.
```

---



---

[← Narrative Arc](../03-narrative-arc.md)  |  [🏠 Home](../README.md)  |  [Module 01: Unity Catalog →](module-01-unity-catalog.md)
