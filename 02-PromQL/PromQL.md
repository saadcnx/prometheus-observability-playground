# im using this fastapi rslim087/fastapi-prometheus:latest in which we can easily get the most common metrics counter gauge histogram

Chapter 1: Getting Started

Prometheus scrapes metrics every 20 seconds
Metrics are saved into Prometheus database
Access Prometheus UI at: localhost:9090 (based on your setup port)

Each metric has:

Help comment - describes what metric is
Type comment - defines metric type

Chapter 2: Metric Types (3 Most Common)

1. Counter Metrics
Values can only ever go up
Example: http_requests_total - total number of HTTP requests
Use labels for dimensionality (e.g., path="/", method="GET")

2. Gauge Metrics
Values go up and down frequently
Example: CPU usage, memory usage, temperature

3. Histogram Metrics
Ideal for request duration measurements
Distributes data into buckets (cumulative)
Example: requests taking <5ms, <10ms, <25ms
Helps understand distribution, not just average

Chapter 3: Basic Queries & Label Selection

1 - Simple Query

promql in prometheus dashboard search paste this ----> http_requests_total
Returns all measurements matching the metric

2 - Label Filtering (Equals)

promql in prometheus dashboard search paste this ----> http_requests_total{path="/"}
promql in prometheus dashboard search paste this ----> http_requests_total{method="GET"}

3 - Multiple Filters

promql in prometheus dashboard search paste this ----> http_requests_total{method="GET", path="/"}

4 - Regex Matching

promql in prometheus dashboard search paste this ----> http_requests_total{path=~"/items.*"}     # Starts with /items
promql in prometheus dashboard search paste this ----> http_requests_total{path=~"/items/1"}     # Specific pattern

5 - Negative/Negated Regex

promql in prometheus dashboard search paste this ----> http_requests_total{path!~"/items.*"}     # Exclude /items paths

Chapter 4: Time Series Concepts

Key Point:
- Prometheus is a Time Series Database
- Each measurement has its own time series with timestamps
- Scraping happens every 20 seconds

Instant Vector
Returns most recent value of each time series
Query without range: http_requests_total

Range Vector
Returns range of values over time for each series
Query with range: http_requests_total[5m] (past 5 minutes)

Chapter 5: Rate Functions

# Rate Function

promql in prometheus dashboard search paste this -----> rate(http_requests_total[5m])

- Expects Range Vector
- Calculates per-second average rate of increase
- Average over entire time window
- Use with counters only

Example: If 1 request every 15 seconds → rate ≈ 0.066 requests/sec

# IRate Function

promql in prometheus dashboard search paste this -----> irate(http_requests_total[5m])

- Calculates rate using only last two data points
- Shows instant rate (spikes, sudden changes)
- Use with counters only

Difference: Rate vs IRate
Rate: Averages over full window (smoother)
IRate: Shows sudden spikes (more sensitive to anomalies)

# Chapter 6: Delta Function (for Gauges)

promql in prometheus dashboard search paste this -----> delta(process_cpu_usage[5m])

Expects Range Vector
Calculates difference between first and last value
Use with gauges and histograms ONLY
Example: CPU decreased by 21% over 5 minutes

⚠️ Don't use rate() with gauges - use delta() instead

Chapter 7: Aggregation Operators
All expect Instant Vector, produce scalar value

Basic Aggregations
promql
sum(http_requests_total)           # Sum all values
avg(http_requests_total)           # Average
max(http_requests_total)           # Maximum
min(http_requests_total)           # Minimum
Grouping (Group By)
promql
sum by (method) (http_requests_total)
Groups aggregation by label values

Produces a Vector (not scalar)

Chaining Grouping with Aggregation
promql
sum(sum by (method) (http_requests_total))
First produces vector grouped by method

Then sums to single scalar

Chapter 8: Aggregation Over Time
Take Range Vector → produce Instant Vector

Functions
promql
sum_over_time(http_requests_total[2m])      # Sum per series
avg_over_time(http_requests_total[2m])      # Average per series
max_over_time(http_requests_total[2m])      # Max per series
min_over_time(http_requests_total[2m])      # Min per series
Chapter 9: Histogram Quantiles
Histogram Quantile Function
promql
histogram_quantile(0.95, sum by (le) (request_duration_buckets{path="/"}))
Steps:

Query histogram buckets

Group by le label (bucket upper bound)

Use sum/avg/min/max by le (doesn't matter which)

Feed into histogram_quantile with percentile (0.95 = 95%)

Interpretation:

0.95 → 95% of requests took less than X ms

0.50 → 50% of requests took less than X ms (median)

Chapter 10: Meaningful Averages & Totals
Average Request Duration
promql
rate(request_duration_sum[2m]) / rate(request_requests_total[2m])
Numerator: Rate of total duration increase

Denominator: Rate of request count increase

Result: Average seconds per request

Increase Function (for Counters)
promql
increase(http_requests_total{path="/"}[3m])
Calculates how much counter increased over time

May extrapolate if window doesn't capture full increase

Larger window = more accurate

Chapter 11: Label Manipulation
Label Replace
promql
label_replace(
    http_requests_total,
    "new_label",      # New label name
    "$1",             # Replacement value (capturing group)
    "path",           # Source label
    "/(.*)"           # Regex pattern with capturing group
)
Creates new labels from existing ones using regex

$1 = first capturing group (anything after slash in this example)

Label Join
promql
label_join(
    http_requests_total,
    "instance_info",  # New label name
    ":",              # Separator
    "instance",       # First label to join
    "job"             # Second label to join
)
Combines multiple label values into one new label

Useful for combined labels for grouping/filtering

Chapter 12: Resource Usage Monitoring
Common Metrics
process_cpu_usage - Current CPU usage percentage

process_resident_memory_bytes - Memory used in bytes

Memory Conversion (Bytes → MB)
promql
process_resident_memory_bytes / 1024 / 1024
÷1024 = kilobytes

÷1024 again = megabytes

Derivative (for Gauges - like rate for gauges)
promql
deriv(process_resident_memory_bytes[1h])
Calculates per-second derivative

Shows rate of change for gauge metrics

Use with gauges only

Chapter 13: Advanced Functions
Top K
promql
topk(5, rate(http_requests_total[5m]))
Returns top K time series with highest values

K = number of results to display

Bottom K
promql
bottomk(5, rate(http_requests_total[5m]))
Returns bottom K time series with lowest values

Quick Reference Table
Function	            Input	        Output	Use With
rate()	                Range Vector	Instant Vector	Counters
irate()	                Range Vector	Instant Vector	Counters
delta()	                Range Vector	Instant Vector	Gauges
increase()	            Range Vector	Instant Vector	Counters
deriv()	                Range Vector	Instant Vector	Gauges
sum/avg/max/min	        Instant Vector	Scalar	Any
sum by()                Instant Vector	Vector	Any
*_over_time()	        Range Vector	Instant Vector	Any
histogram_quantile()	Vector	Scalar	Histograms
topk()/bottomk()	    Instant Vector	Vector	Any

Key Rules to Remember
Rate/IRate → Counters only
Delta/Deriv → Gauges only
Instant Vector = single value per time series
Range Vector = range of values per time series

Labels are case-sensitive
Histogram buckets are cumulative
Increase function may extrapolate for small windows

