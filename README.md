# API Investigation Practice: Testing for Silent Failures

## Setup

To build hands-on investigation skills for API and log-based troubleshooting, I used JSONPlaceholder, a public mock REST API, as a stand-in target. A mock API isn't a limitation here, it's actually useful for this kind of practice: it has zero validation, zero auth enforcement, and zero rate limiting, which means every test I write has to be based on what *correct* behavior should look like, not on what the tool happens to do. That gap between "expected" and "actual" is where the real investigation work happens.

For each scenario below, I formed a hypothesis before sending the request, ran it, and compared the result against what I expected. Where the result matched, I confirmed the mechanism. Where it didn't, that mismatch became the finding.

## Scenario 1: Missing Required Field

**Test**: Sent a POST request with a required field (`title`, later reframed as `cardNumber` in a payment context) deliberately omitted from the body.

**Result**: The API returned `201 Created` and echoed back exactly the fields I sent, no `title`, no placeholder, no error. The field simply wasn't there.

**What this means**: On a real system, this pattern points to two separate questions that need to be investigated independently. First, is the data actually missing at the source, check the sender's outbound payload before anything else, since that's the cheapest and most isolated check. Second, and separately, why did the receiving system accept an incomplete request without complaint. The sender is the immediate lead for root cause. But a receiving system with no validation on mandatory fields is its own finding, and the actual fix, since it means bad data can pass through silently every time, not just this once.

## Scenario 2: Authentication Enforcement

**Test**: Sent a request with a deliberately invalid bearer token, and wrote a test asserting the response should return `401 Unauthorized`.

**Result**: The API returned `200 OK`, ignoring the token entirely. The test failed.

**What this means**: This failure isn't a broken test, it's the correct test producing an accurate, serious finding. On a real system, if the same test failed this way, it wouldn't mean "the auth rules have a bug." It would mean there is no authentication enforcement on this endpoint at all, and anyone, with or without valid credentials, can access whatever this endpoint exposes. That's a finding to escalate immediately, not log as a minor defect.

## Scenario 3: Rate Limiting

**Test**: Sent the same request 15-20 times in rapid succession, with a test asserting `429 Too Many Requests`.

**Result**: Every request returned `200 OK`. The test failed consistently across the whole burst.

**What this means**: On a real system, a failure here specifically points to two risks, not a vague "vulnerability to attack." First, if this were a payment endpoint, no rate limiting means an attacker could attempt to brute-force card details or repeatedly probe the endpoint with no friction. Second, and just as likely in practice, a legitimate integration with a bug could accidentally flood the endpoint from a single source with no system in place to slow it down or flag it.

## What This Demonstrates

The throughline across all three scenarios is the same: I wrote each test to describe correct behavior first, based on how a well-built system *should* respond, not based on what I expected this particular mock to do. Where the test failed, I treated that failure as a real signal worth interpreting, rather than assuming a failing test meant broken test logic. Distinguishing a meaningful failure (the response genuinely doesn't match correct behavior) from a broken test (the assertion could never have passed to begin with, regardless of system state) was itself part of the exercise, and it's the habit I'd carry directly into investigating a real PMS booking-sync or payment integration failure.
