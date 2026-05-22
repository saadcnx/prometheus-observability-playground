# Prometheus Aggregation Operators — The Powerhouse

When you have thousands of pods/servers in production, you can't look at individual lines. You need to **aggregate** (squeeze) the data.

---

## ⚠️ Small Correction: Vector vs Scalar

**Notes say:** "All expect Instant Vector, produce scalar value"

**Reality:** When you use `sum()`, `avg()`, `max()`, `min()` without grouping, the result is still an **Instant Vector** — just with a single element.

**Scalar** in Prometheus means a plain number with no labels, time, or series attached (e.g., just the number `5` or `21`).

`sum(http_requests_total)` gives you a vector where all series are combined into one display.

---

## 1. Basic Aggregations — Combine Everything

Your app runs on 3 servers (Pods):

| Pod | Requests |
|-----|----------|
| A   | 10       |
| B   | 20       |
| C   | 30       |

**Queries & Results:**

| Function | Math | Result |
|----------|------|--------|
| `sum(http_requests_total)` | 10 + 20 + 30 | **60** |
| `avg(http_requests_total)` | (10+20+30)/3 | **20** |
| `max(http_requests_total)` | Highest | **30** |
| `min(http_requests_total)` | Lowest | **10** |

---

## 2. Grouping — `sum by (label)`

Like SQL's `GROUP BY`. Use the `by` keyword to break down data by a label.

**Example:** Labels have `method` (GET or POST).

| Pod | GET | POST |
|-----|-----|------|
| A   | 5   | 5    |
| B   | 15  | 5    |

**Query:**
```promql
sum by (method) (http_requests_total)
Result: Prometheus ignores Pods and groups only by method:

{method="GET"}  → 20
{method="POST"} → 10

Two separate lines (Vector) returned.
```

## 3. Chaining Grouping with Aggregation (Double Layering)
Example from notes:
sum(sum by (method) (http_requests_total))

How it works:

Inner part runs first: sum by (method) → GET: 20, POST: 10
Outer sum() adds those results: 20 + 10 = 30

DevOps Pro Tip : If you just want a single total, sum(http_requests_total) gives you 30 directly. Nesting is useful when you apply complex functions (like rate) first, then squeeze further.

## Real-World Practical Example 

Combining rate and sum to see traffic in a cluster:

sum by (status_code) (rate(http_requests_total[5m]))

Translation:
Take the per-second rate of requests over the last 5 minutes
Group them by status_code (200, 404, 500)
Show separate lines for each status code
