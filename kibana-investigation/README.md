# Kibana Investigation: Web Log Analysis and Spike Detection

## Setup

To build log aggregation skills for real incident investigation, I used Elastic Cloud (hosted Kibana/Elasticsearch) with a sample web server access log dataset. The investigation focus was on the core skill set for support roles: quickly identifying whether error rates represent a real incident or normal statistical variation, and if there's a problem, using aggregations to narrow scope before drilling into individual log lines.

Web logs are a common data source in support work, whether investigating integration failures (HTTP status codes, response times), infrastructure issues (error rates by endpoint), or user impact (request volume spikes).

## Chart 1: Time-Series Error Rate Over Time

Built a stacked bar chart showing total request count per hour, broken down by status code (2xx, 4xx, 5xx). Over a two-week window, the chart showed overall flat traffic with occasional small fluctuations in the error bars.

**Finding**: No meaningful spikes. Error counts ranged from roughly 2-12 per hour bucket, consistent with normal background statistical noise. Total requests stayed stable around 10-30 per hour, with error rate holding steady at roughly 8% (matching the population average).

**What this reveals**: A flat time-series rules out the "something broke at a specific point" scenario. If this were a real incident report ("error rate spiked today"), this chart would immediately tell you to look elsewhere, there's no evidence of a discrete failure event in the data.

## Chart 2: Request Breakdown by Category

Built a bar chart grouping requests by response status code, showing count for each. Results: roughly 1,600 requests total, with 92% returning 200-level success, the remaining 8% distributed across various error codes.

**What this reveals**: This is what "normal" looks like for healthy web traffic. A system returning 20% errors would be actively broken and causing widespread impact. This baseline is essential for comparison, knowing what undisturbed traffic looks like makes it obvious when something's genuinely wrong.

## Dashboard and Interactive Filtering

Combined both charts into a single dashboard, then used Kibana's time-range filtering to select individual spike windows and watch the category breakdown update accordingly. Testing showed that even the visible peaks (those 12-count bump hours) had proportionally identical error distributions to the overall average, confirming they were noise, not anomalies.

**Critical observation**: The rightmost time bucket on the chart was noticeably shorter than others, a key pattern to recognize. This bucket represents an incomplete time period (current hour still filling with live data), not a genuine drop in traffic. Misreading this as "traffic just dropped" is one of the most common false alarms in live log monitoring, worth explicitly checking every time.

## What This Demonstrates

The investigation demonstrated the core triage pattern in log analysis: start with the aggregate view (time-series + breakdowns), form a hypothesis about whether something's wrong (spike detection), then compare against the normal range for that specific system. In this case, the hypothesis was "a spike visible in the time chart points to an incident," the data confirmed "no meaningful spikes exist," conclusion: no incident. A real problem would have shown a clear deviation from baseline, making the next step (filtering to the affected window and reading individual log lines) obvious and focused, rather than hunting through undifferentiated noise.
