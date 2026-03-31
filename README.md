# 📊 Olist SQL Portfolio

---

## 1. How many total orders are there?

```sql
SELECT COUNT(*) AS total_orders
FROM orders;
```

Insight: Total orders indicate the scale of platform activity.

---

## 2. What is the total revenue (GMV)?

```sql
SELECT 
    ROUND(SUM(price + freight_value), 2) AS total_gmv
FROM order_items oi
JOIN orders o ON oi.order_id = o.order_id
WHERE o.order_status = 'delivered';
```

Insight: GMV shows total business value generated.

---

## 3. What is the monthly sales trend?

```sql
SELECT
    STRFTIME('%Y-%m', o.order_purchase_timestamp) AS month,
    ROUND(SUM(oi.price), 2) AS revenue
FROM orders o
JOIN order_items oi ON o.order_id = oi.order_id
WHERE o.order_status = 'delivered'
GROUP BY month
ORDER BY month;
```

Insight: Helps identify growth trends and seasonality.

---

## 4. Which product categories generate the most revenue?

```sql
SELECT
    t.product_category_name_english AS category,
    ROUND(SUM(oi.price), 2) AS revenue
FROM order_items oi
JOIN orders o ON oi.order_id = o.order_id
JOIN products p ON oi.product_id = p.product_id
LEFT JOIN product_category_name_translation t
ON p.product_category_name = t.product_category_name
WHERE o.order_status = 'delivered'
GROUP BY category
ORDER BY revenue DESC
LIMIT 10;
```

Insight: Identifies top-performing product categories.

---

## 5. What is the on-time delivery rate?

```sql
SELECT
    ROUND(
        SUM(CASE 
            WHEN order_delivered_customer_date <= order_estimated_delivery_date 
            THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2
    ) AS on_time_percentage
FROM orders
WHERE order_status = 'delivered';
```

Insight: Measures logistics performance and customer satisfaction.

---

## 6. How many customers are repeat buyers?

```sql
SELECT
    purchase_count,
    COUNT(*) AS customers
FROM (
    SELECT
        c.customer_unique_id,
        COUNT(DISTINCT o.order_id) AS purchase_count
    FROM customers c
    JOIN orders o ON c.customer_id = o.customer_id
    WHERE o.order_status = 'delivered'
    GROUP BY c.customer_unique_id
) t
GROUP BY purchase_count;
```

Insight: Shows customer loyalty and retention patterns.

---

## 7. How does delivery speed affect review scores?

```sql
SELECT
    CASE
        WHEN JULIANDAY(order_delivered_customer_date) - JULIANDAY(order_purchase_timestamp) <= 5 THEN 'Fast'
        WHEN JULIANDAY(order_delivered_customer_date) - JULIANDAY(order_purchase_timestamp) <= 15 THEN 'Medium'
        ELSE 'Slow'
    END AS delivery_speed,
    ROUND(AVG(r.review_score), 2) AS avg_review
FROM orders o
LEFT JOIN order_reviews r ON o.order_id = r.order_id
WHERE o.order_status = 'delivered'
GROUP BY delivery_speed;
```

Insight: Faster delivery leads to better customer reviews.

---

## 8. What are the order statuses distribution?

```sql
SELECT
    order_status,
    COUNT(*) AS order_count
FROM orders
GROUP BY order_status
ORDER BY order_count DESC;
```

Insight: Shows operational distribution of orders.

---

## 9. What is the average order value (AOV)?

```sql
SELECT
    ROUND(SUM(oi.price) / COUNT(DISTINCT o.order_id), 2) AS avg_order_value
FROM order_items oi
JOIN orders o ON oi.order_id = o.order_id
WHERE o.order_status = 'delivered';
```

Insight: Measures revenue efficiency per order.

---

## 10. How many unique customers are there?

```sql
SELECT COUNT(DISTINCT customer_unique_id) AS unique_customers
FROM customers;
```

Insight: Indicates total customer base size.

---

## 11. Which states generate the most revenue?

```sql
SELECT
    c.customer_state,
    ROUND(SUM(oi.price), 2) AS revenue
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_items oi ON o.order_id = oi.order_id
WHERE o.order_status = 'delivered'
GROUP BY c.customer_state
ORDER BY revenue DESC;
```

Insight: Identifies top geographic markets.

---

## 12. What are the most used payment methods?

```sql
SELECT
    payment_type,
    COUNT(*) AS usage_count
FROM order_payments
GROUP BY payment_type
ORDER BY usage_count DESC;
```

Insight: Shows customer payment preferences.

---

## 13. What is the average delivery time (days)?

```sql
SELECT
    ROUND(AVG(
        JULIANDAY(order_delivered_customer_date) - JULIANDAY(order_purchase_timestamp)
    ), 2) AS avg_delivery_days
FROM orders
WHERE order_status = 'delivered';
```

Insight: Measures logistics efficiency.

---

## 14. Which sellers generate the highest revenue?

```sql
SELECT
    oi.seller_id,
    ROUND(SUM(oi.price), 2) AS revenue
FROM order_items oi
JOIN orders o ON oi.order_id = o.order_id
WHERE o.order_status = 'delivered'
GROUP BY oi.seller_id
ORDER BY revenue DESC
LIMIT 10;
```

Insight: Identifies top-performing sellers.

---

## 15. What is the freight cost percentage of product price?

```sql
SELECT
    ROUND(AVG(freight_value / price) * 100, 2) AS freight_percentage
FROM order_items;
```

Insight: Shows logistics cost relative to product value.

---
