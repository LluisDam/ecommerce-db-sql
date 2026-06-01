# 🛒 E-Commerce Database — SQL

![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-003B57?style=for-the-badge&logo=sqlite&logoColor=white)

> Production-ready relational database design for an e-commerce platform. Includes normalized schema, views, stored procedures, triggers, and analytical queries.
>
> ---
>
> ## 🚀 What's Included
>
> - **Normalized Schema** — 3NF relational model for users, products, orders, payments and reviews
> - - **Views** — Pre-built views for dashboards and reporting
>   - - **Stored Procedures** — Encapsulated business logic (order placement, stock updates)
>     - - **Triggers** — Automatic audit logging and stock management on DML events
>       - - **Indexes** — Performance-tuned indexes on high-traffic columns
>         - - **Analytical Queries** — Sales reports, cohort analysis, top products, and revenue trends
>          
>           - ---
>
> ## 🗄️ Schema Overview
>
> ```
> Tables:
> ├── users              — Customer and admin accounts
> ├── addresses          — Shipping/billing addresses (1:N with users)
> ├── categories         — Product category hierarchy (self-referencing)
> ├── products           — Product catalog with stock and pricing
> ├── product_images     — Multiple images per product
> ├── orders             — Order header (status, total, timestamps)
> ├── order_items        — Line items linking orders to products
> ├── payments           — Payment records (method, status, gateway ref)
> ├── reviews            — Product reviews with rating and moderation
> ├── coupons            — Discount codes with expiry and usage limits
> └── audit_log          — Automatic audit trail (via triggers)
> ```
>
> ---
>
> ## 📊 Views
>
> | View | Description |
> |------|-------------|
> | `v_product_summary` | Products with category name, stock status and average rating |
> | `v_order_details` | Full order info with customer name and line items |
> | `v_monthly_revenue` | Revenue aggregated by month and category |
> | `v_top_customers` | Ranked customers by lifetime spend |
> | `v_low_stock_alert` | Products below minimum stock threshold |
>
> ---
>
> ## ⚙️ Stored Procedures
>
> ```sql
> -- Place a new order atomically
> CALL sp_place_order(user_id, coupon_code, address_id, items_json);
>
> -- Process a return and restore stock
> CALL sp_process_return(order_id, reason);
>
> -- Apply seasonal discount to a category
> CALL sp_apply_category_discount(category_id, discount_pct, valid_until);
> ```
>
> ---
>
> ## 🔔 Triggers
>
> | Trigger | Event | Action |
> |---------|-------|--------|
> | `trg_stock_on_order` | After INSERT on `order_items` | Decrements product stock |
> | `trg_stock_on_return` | After UPDATE on `orders` (status→returned) | Restores product stock |
> | `trg_audit_products` | After UPDATE/DELETE on `products` | Logs change to `audit_log` |
> | `trg_review_avg` | After INSERT/UPDATE on `reviews` | Updates product average rating |
>
> ---
>
> ## 📈 Sample Analytical Queries
>
> ```sql
> -- Monthly revenue by category (last 12 months)
> SELECT DATE_FORMAT(o.created_at, '%Y-%m') AS month,
>        c.name AS category,
>        SUM(oi.quantity * oi.unit_price) AS revenue
> FROM orders o
> JOIN order_items oi ON o.id = oi.order_id
> JOIN products p ON oi.product_id = p.id
> JOIN categories c ON p.category_id = c.id
> WHERE o.status = 'completed'
>   AND o.created_at >= DATE_SUB(NOW(), INTERVAL 12 MONTH)
> GROUP BY month, category
> ORDER BY month DESC, revenue DESC;
>
> -- Customer cohort retention (monthly)
> SELECT cohort_month,
>        activity_month,
>        COUNT(DISTINCT user_id) AS active_users
> FROM (
>   SELECT user_id,
>          DATE_FORMAT(MIN(created_at) OVER (PARTITION BY user_id), '%Y-%m') AS cohort_month,
>          DATE_FORMAT(created_at, '%Y-%m') AS activity_month
>   FROM orders
> ) cohorts
> GROUP BY cohort_month, activity_month
> ORDER BY cohort_month, activity_month;
> ```
>
> ---
>
> ## 🗂️ File Structure
>
> ```
> ecommerce-db-sql/
> ├── schema/
> │   ├── 01_tables.sql          # Table definitions (DDL)
> │   ├── 02_indexes.sql         # Index creation
> │   ├── 03_views.sql           # View definitions
> │   ├── 04_procedures.sql      # Stored procedures
> │   └── 05_triggers.sql        # Trigger definitions
> ├── seed/
> │   ├── seed_categories.sql    # Sample categories
> │   ├── seed_products.sql      # 500 sample products
> │   └── seed_orders.sql        # 2 years of sample orders
> ├── queries/
> │   ├── sales_reports.sql      # Revenue and sales analytics
> │   ├── customer_analysis.sql  # Cohort and segmentation queries
> │   └── product_performance.sql
> └── README.md
> ```
>
> ---
>
> ## ⚙️ Setup
>
> ```bash
> # MySQL
> mysql -u root -p < schema/01_tables.sql
> mysql -u root -p < schema/02_indexes.sql
> mysql -u root -p < schema/03_views.sql
> mysql -u root -p < schema/04_procedures.sql
> mysql -u root -p < schema/05_triggers.sql
>
> # Load sample data
> mysql -u root -p ecommerce_db < seed/seed_categories.sql
> mysql -u root -p ecommerce_db < seed/seed_products.sql
> ```
>
> ---
>
> ## 📄 License
>
> MIT License — feel free to use this project as a reference or starting point.
>
> ---
>
> *Developed by [Lluis Soberats](https://github.com/LluisDam) — Project Manager & Database Designer*
