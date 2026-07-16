# SQL Fundamentals

## Concept
SQL for data science interviews isn't just "can you write a SELECT" — it's "can you translate a business question into the right joins and aggregations." The core building blocks: `SELECT/WHERE/GROUP BY/HAVING/ORDER BY`, joins (INNER vs LEFT vs FULL), and knowing *when* to use each one without hesitating.

The distinction that trips people up: `WHERE` filters rows before grouping, `HAVING` filters groups after aggregation.

## Example
"Find customers who placed more than 3 orders":
```sql
SELECT customer_id, COUNT(order_id) AS order_count
FROM orders
GROUP BY customer_id
HAVING COUNT(order_id) > 3;
```
Note: you can't write `WHERE COUNT(order_id) > 3` — `COUNT` doesn't exist yet at the row-filtering stage, only after grouping. That's why `HAVING` exists.

## Common interview question
**Q:** What's the difference between INNER JOIN and LEFT JOIN?
**A:** INNER JOIN returns only rows where there's a match in both tables. LEFT JOIN returns all rows from the left table, plus matched rows from the right table (NULL where there's no match). Use LEFT JOIN when you need to preserve records even if they have no corresponding data — e.g. "all customers, including those with zero orders."

## Gotchas / things I got wrong first
- Forgetting that `NULL` isn't equal to anything, even `NULL` itself — so `WHERE column = NULL` never works; you need `WHERE column IS NULL`.
- Putting a filter condition in the `ON` clause vs `WHERE` clause of a LEFT JOIN gives different results — `ON` filters before the join happens (keeps unmatched left rows), `WHERE` filters after (can silently drop them).