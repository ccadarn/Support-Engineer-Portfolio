# Support Engineering Investigation Portfolio

This portfolio demonstrates hands-on investigative methodology across API troubleshooting, relational data analysis, and log aggregation, the core skills for Tier 2/3 support engineering roles. Each investigation uses realistic failure scenarios to showcase how to isolate root causes, correlate data across systems, and communicate findings clearly.

## Investigations

### Postman: API Integration Failure Testing
**What it demonstrates**: Request/response analysis, test scripting, distinguishing between meaningful failures and broken tests, mock API behavior interpretation.

[View investigation](./postman-investigation/README.md)

Key findings: Silent acceptance of incomplete data, authentication enforcement gaps, rate limiting exposure. Tests were written to express correct behavior first, allowing actual system behavior to surface real vulnerabilities.

### SQL: Booking-to-Payment Data Correlation
**What it demonstrates**: Multi-table joins, NULL handling in SQL, compound WHERE logic, identifying data consistency issues at system boundaries, triage-focused query design.

[View investigation](./sql-investigation/README.md)

Key findings: Missing payment records, amount mismatches, and 100% correlation between sync failures and upstream payment problems. Each failure type points to a different root cause and requires a different investigation path.

### Kibana: Web Log Analysis and Spike Detection
**What it demonstrates**: Time-series aggregation, breakdowns and filtering, distinguishing signal from noise, incomplete-bucket interpretation in live data, triage through visualization.

[View investigation](./kibana-investigation/README.md)

Key findings: Error rates remained stable background noise over a two-week window, no incident indicators. Also demonstrates the pattern of spike detection as a *first step* toward root-cause investigation, not a substitute for it.

---

The methodology consistent across all three: form a hypothesis before running queries/tests, execute, compare actual results against prediction, and extract specific, actionable findings rather than general observations.
