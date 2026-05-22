# histogram_quantile — The Ultimate SRE Tool

Welcome to the most advanced level of Prometheus! `histogram_quantile` is what SREs and DevOps engineers use most in production to check actual app performance (SLA/SLO).

---

**Only `sum` can be used!** You can NEVER use `avg`, `min`, or `max` with `histogram_quantile`.
**Why:** Histogram buckets are **Counters** (learned in Chapter 4 & 5). When combining buckets from different pods/instances, you **must sum** the counters to preserve the bucket's mathematical structure. Using `avg` or `max` destroys the histogram formula — Prometheus will either throw an error or give garbage values.

---

## What is a Quantile/Percentile? 

Average lies. Always.

**Example:** 9 users get a response in 10ms. 1 user's request gets stuck and takes 10,000ms (10 seconds).

- Average ≈ 1000ms → Makes it look like everyone's app is slow → **Lie!**

**Quantiles tell the truth:**

| Quantile | Meaning |
|----------|---------|
| **0.50 (50th percentile / Median)** | 50% of users got response time ≤ this value |
| **0.95 (95th percentile)** | 95% of users got response time ≤ this value. Only 5% unlucky users took longer |

---

## Step-by-Step Query Breakdown 🛠️

**The Query:**
```promql
histogram_quantile(0.95, sum by (le) (rate(request_duration_seconds_bucket[5m])))

Note: In the real world, always apply rate() first to capture the last 5-minute trend, then sum.
```

Step 1: Grab the Bucket Metric
When you create a histogram in Prometheus, it generates a metric ending with _bucket (standard name).


Step 2: The Magic of le Label
This metric has a special label: le (Less than or Equal to) — the upper bound of each bucket.

le Value	Meaning
le="0.1"	Requests completed in less than 0.1 seconds
le="0.5"	Requests completed in less than 0.5 seconds
le="+Inf"	All requests (everything)


Step 3: sum by (le) — Strict Rule!
Since many pods run in a cluster, we combine same buckets across all pods:

promql
sum by (le) (...)
Prometheus adds together all counters with the same le label (e.g., all le="0.1" buckets get summed).

Only sum works here. Never avg, min, or max!

Step 4: histogram_quantile() Applies the Formula
Now all this data is passed to histogram_quantile(0.95, ...). Prometheus uses mathematical interpolation to find the exact point (in ms/seconds) where 95% of users' journeys were completed.

Real-World Interpretation 💡
Query result: 0.25 (seconds)

How to read this on a dashboard:

"My application is running very smooth. In the last 5 minutes, 95% of requests were processed in less than 0.25 seconds (250ms). Only 5% of requests took longer than 250ms."




