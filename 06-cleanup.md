[← Module 05: Governance Proof](modules/module-05-governance-proof.md)  |  [🏠 Home](README.md)  |  [Customer Workshop Variant →](07-customer-workshop-variant.md)

---

# SECTION 6: Cleanup Module

**Time estimate:** 5 minutes

Drop all objects in reverse creation order:

```sql
-- Drop Genie Agent: use the Databricks UI (Genie > RAD_GENIE_STORE_SALES > Delete)
-- No SQL DROP command exists for Genie Agents — they are managed via UI/API only.

-- Drop tables
DROP TABLE IF EXISTS RAD_CATALOG.RAD_FOUNDATION.RAD_PRODUCT_REVIEWS;
DROP TABLE IF EXISTS RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS;
DROP TABLE IF EXISTS RAD_CATALOG.RAD_FOUNDATION.RAD_SALES_TXN;
DROP TABLE IF EXISTS RAD_CATALOG.RAD_FOUNDATION.RAD_SKU_LOOKUP;
DROP TABLE IF EXISTS RAD_CATALOG.RAD_FOUNDATION.RAD_STORE;

-- Drop schemas
DROP SCHEMA IF EXISTS RAD_CATALOG.RAD_AGENTS;
DROP SCHEMA IF EXISTS RAD_CATALOG.RAD_FOUNDATION;

-- Drop catalog
DROP CATALOG IF EXISTS RAD_CATALOG;

-- If you created account-level groups only for this lab, delete them in the
-- account console or via SCIM/API after the session.
-- Do not use DROP GROUP here: that statement applies only to legacy
-- workspace-local groups and is not part of a Unity Catalog setup.
```

**Final verification:**
```sql
SHOW CATALOGS LIKE 'RAD_CATALOG';
-- Expected: 0 rows returned, confirming full cleanup
```

**Note:** Run cleanup before logging out to prevent naming conflicts for future participants. Delete the Genie Agent manually via the UI first — this must happen before schema cleanup, since the agent references these tables.

---



---

[← Module 05: Governance Proof](modules/module-05-governance-proof.md)  |  [🏠 Home](README.md)  |  [Customer Workshop Variant →](07-customer-workshop-variant.md)
