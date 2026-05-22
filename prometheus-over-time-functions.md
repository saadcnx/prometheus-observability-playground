# _over_time Functions — Vertical Aggregation

## Core Concept: What _over_time Does

In the last chapter, `sum()` combined different servers/pods (Horizontal Aggregation).

But `sum_over_time()` and `avg_over_time()` **don't touch other servers**. They take one server's past data points and add/average them (Vertical/Time Aggregation).

- **Takes Range Vector → Produces Instant Vector:** Always needs a time window like `[2m]`. It grabs all raw points in that window, calculates, and returns a single instant value.

---

## Practical Example 📊

One server (Pod-A) is sending metrics. Scraping happens every **20 seconds**, so in **2 minutes [2m]** you get **6 data points**.

Pod-A's `http_requests_total` over the last 2 minutes:

[10, 12, 15, 15, 18, 20]

---

### 1. `sum_over_time(http_requests_total[2m])`

**What it does:** Adds up all 6 points of Pod-A.

**Math:** `10 + 12 + 15 + 15 + 18 + 20 = 90`

---

### 2. `avg_over_time(http_requests_total[2m])`

**What it does:** Averages those 6 points.

**Math:** `90 / 6 = 15`

**Real Use Case:** Very common with Gauges. "What was the average CPU in the last hour?"
```promql
avg_over_time(process_cpu_usage[1h])


```

3. max_over_time(http_requests_total[2m])

What it does: Returns the highest value in the last 2 minutes.

Result: 20

Real Use Case: Track memory usage. "What was the maximum RAM my app used in the last 24 hours?"

promql
max_over_time(go_memstats_alloc_bytes[24h])

```

4. min_over_time(http_requests_total[2m])

What it does: Returns the smallest value in the last 2 minutes.

Result: 10

```

Golden Rule — Remember the Difference

sum(...)	Combine all servers, show me the total right now. (Servers become 1)
sum_over_time(...[5m])	For each server, show me its own total over the last 5 minutes. (Servers stay the same, past time gets squeezed)
