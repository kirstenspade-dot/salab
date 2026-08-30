[← Overview](01-lab-overview.md)  |  [🏠 Home](README.md)  |  [Narrative Arc →](03-narrative-arc.md)

---

# SECTION 2: Fictional Company Profile

### 2a. Company Profile

**Company:** Radiance Beauty Co. (abbreviation: **RAD**)

Radiance Beauty Co. is a 900-store specialty beauty retailer operating across the US, similar in scale and footprint to a national beauty chain. RAD sells cosmetics, skincare, and haircare across owned brands and third-party lines, both in-store and online.

**Specific pain point this lab addresses:** RAD's sales leaders need answers to store-level sales questions — "which stores are underperforming this week," "how did our new skincare line do in the Southeast region," "what's our average basket size at stores over 5,000 sq ft" — but the data lives in three disconnected systems: BigQuery (legacy reporting), Databricks (where the advanced ML/forecasting work happens), and SAS Viya (basic analytics). Getting an answer today means routing a request through the analytics team and waiting, sometimes days. RAD's ops team tried shortcutting this with a general-purpose AI chatbot (Claude, used directly against exported data with no governance layer), but got inconsistent answers with no way to verify which numbers were trustworthy or current. RAD needs one governed, natural-language interface, grounded in a single source of truth, that a sales leader can query directly.

### 2b. Data Model

**RAD_STORE** (entity/dimension)
| Column | Type | Description |
|---|---|---|
| store_id | STRING | Unique store identifier |
| store_name | STRING | Store display name |
| region | STRING | Sales region (Northeast, Southeast, Midwest, West) |
| state | STRING | Two-letter state code |
| sq_footage | INT | Store square footage |
| open_date | DATE | Store opening date |

Sample rows:
```
store_id | store_name        | region     | state | sq_footage | open_date
RAD-1001 | Radiance Buckhead | Southeast  | GA    | 4200       | 2018-03-14
RAD-1002 | Radiance SoHo     | Northeast  | NY    | 6800       | 2015-09-01
RAD-1003 | Radiance Naperville | Midwest  | IL    | 3900       | 2020-11-20
RAD-1004 | Radiance Scottsdale | West     | AZ    | 5100       | 2019-06-05
RAD-1005 | Radiance Dilworth | Southeast  | NC    | 3600       | 2021-02-17
```

**RAD_SALES_TXN** (transactions/events)
| Column | Type | Description |
|---|---|---|
| txn_id | STRING | Unique transaction identifier |
| store_id | STRING | FK to RAD_STORE |
| sku_id | STRING | FK to RAD_SKU_LOOKUP |
| sale_date | DATE | Date of sale |
| quantity | INT | Units sold |
| sale_amount | DECIMAL(10,2) | Total sale amount, USD |
| channel | STRING | "in_store" or "online_pickup" |

Sample rows:
```
txn_id   | store_id | sku_id  | sale_date  | quantity | sale_amount | channel
TXN-9001 | RAD-1001 | SKU-441 | 2026-07-01 | 1        | 42.00       | in_store
TXN-9002 | RAD-1002 | SKU-119 | 2026-07-01 | 2        | 76.50       | in_store
TXN-9003 | RAD-1001 | SKU-220 | 2026-07-02 | 1        | 28.00       | online_pickup
TXN-9004 | RAD-1004 | SKU-441 | 2026-07-02 | 3        | 126.00      | in_store
TXN-9005 | RAD-1003 | SKU-119 | 2026-07-03 | 1        | 38.25       | in_store
```

**RAD_STORE_METRICS** (analytics/metrics)
| Column | Type | Description |
|---|---|---|
| store_id | STRING | FK to RAD_STORE |
| metric_date | DATE | Date of metric snapshot |
| daily_sales | DECIMAL(10,2) | Total sales for the day |
| conversion_rate | DECIMAL(5,4) | Foot traffic to purchase conversion |
| foot_traffic | INT | Store visitor count |
| avg_basket_size | DECIMAL(10,2) | Average transaction value |

Sample rows:
```
store_id | metric_date | daily_sales | conversion_rate | foot_traffic | avg_basket_size
RAD-1001 | 2026-07-01  | 3120.00     | 0.2810           | 340          | 40.60
RAD-1002 | 2026-07-01  | 5980.50     | 0.3120           | 512          | 44.15
RAD-1003 | 2026-07-01  | 1890.00     | 0.2205           | 275          | 31.20
RAD-1004 | 2026-07-01  | 4410.00     | 0.2990           | 398          | 38.90
RAD-1005 | 2026-07-01  | 2205.75     | 0.2450           | 290          | 33.10
```

**RAD_PRODUCT_REVIEWS** (documents/unstructured)
| Column | Type | Description |
|---|---|---|
| review_id | STRING | Unique review identifier |
| sku_id | STRING | FK to RAD_SKU_LOOKUP |
| review_text | STRING | Free-text customer review |
| sentiment_score | DECIMAL(3,2) | -1.00 to 1.00 |
| review_date | DATE | Date review submitted |

Sample rows:
```
review_id | sku_id  | review_text                                        | sentiment_score | review_date
REV-501   | SKU-441 | "Best moisturizer I've used, absorbs fast"          | 0.92            | 2026-06-28
REV-502   | SKU-119 | "Good but the pump breaks after a month"            | 0.15            | 2026-06-29
REV-503   | SKU-220 | "Love the scent, will repurchase"                   | 0.85            | 2026-06-30
REV-504   | SKU-441 | "Too greasy for my skin type"                       | -0.40           | 2026-07-01
REV-505   | SKU-119 | "Perfect for sensitive skin, no irritation at all"  | 0.88            | 2026-07-01
```

**RAD_SKU_LOOKUP** (reference/lookup)
| Column | Type | Description |
|---|---|---|
| sku_id | STRING | Unique SKU identifier |
| product_name | STRING | Product display name |
| category | STRING | skincare / haircare / cosmetics |
| brand | STRING | Brand name |
| price | DECIMAL(10,2) | List price |

Sample rows:
```
sku_id  | product_name           | category  | brand       | price
SKU-441 | Hydra Glow Moisturizer | skincare  | Lumine      | 42.00
SKU-119 | Silk Repair Serum      | haircare  | Verdant     | 38.25
SKU-220 | Matte Finish Foundation| cosmetics | Radiance Co | 28.00
SKU-330 | Overnight Renewal Mask | skincare  | Lumine      | 34.00
SKU-410 | Volumizing Mousse      | haircare  | Verdant     | 22.50
```

### 2c. Role Structure

- **RAD_ADMIN** — full admin, runs Module 00 setup, grants roles
- **RAD_ANALYST** — the Genie Agent curator role; configures the agent, writes example SQL, annotates columns
- **RAD_BUSINESS_USER** — the sales leader persona; only queries the Genie Agent through the chat UI, never sees SQL

### 2d. Naming Convention Reference Table

All objects follow `RAD_[OBJECT_TYPE]`.

| Object | Convention | Example |
|---|---|---|
| Catalog | RAD_CATALOG | RAD_CATALOG |
| Schema (foundation) | RAD_FOUNDATION | RAD_CATALOG.RAD_FOUNDATION |
| Schema (agent config) | RAD_AGENTS | RAD_CATALOG.RAD_AGENTS |
| Warehouse | RAD_WH | RAD_WH |
| Genie Agent | RAD_GENIE_[DOMAIN] | RAD_GENIE_STORE_SALES |
| Role | RAD_[ROLE] | RAD_ANALYST |

### 2e. Industry Swappability Guide

To adapt this lab to a different industry, change:
- `RAD_STORE` → equivalent entity table (e.g., `HC_CLINIC` for healthcare, `FS_BRANCH` for financial services)
- `RAD_SALES_TXN` → equivalent transaction table (e.g., `HC_CLAIM`, `FS_TRANSACTION`)
- `RAD_PRODUCT_REVIEWS` → equivalent unstructured/document table (e.g., `HC_PATIENT_NOTES`, `FS_ADVISOR_NOTES`)

**Worked example — Financial Services:** Replace `RAD_STORE` with `FS_BRANCH` (branch_id, branch_name, region, state, aum, open_date). Replace `RAD_SALES_TXN` with `FS_TRANSACTION` (txn_id, branch_id, account_id, txn_date, amount, txn_type). Replace `RAD_PRODUCT_REVIEWS` with `FS_ADVISOR_NOTES` (note_id, account_id, note_text, sentiment_score, note_date). The business problem becomes: "Branch managers need answers to book-of-business questions, but data is split across a legacy CRM, Databricks, and a compliance reporting tool."

---



---

[← Overview](01-lab-overview.md)  |  [🏠 Home](README.md)  |  [Narrative Arc →](03-narrative-arc.md)
