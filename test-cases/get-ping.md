# GET /ping — Health Check Test Cases

**Endpoint:** `GET /ping`

**Purpose:** Verify that the API health check endpoint is available and returns a stable response.

- ✅ TC-PING-001 — Positive: health check endpoint is reachable
- ✅ TC-PING-002 — Negative: unsupported method on /ping
- ✅ TC-PING-003 — Negative: invalid path
- ✅ TC-PING-004 — Edge: repeated calls return stable result


## TC-PING-001 — Positive: health check endpoint is reachable

**Priority:** High  
**Type:** Positive / Smoke \
**Preconditions:** API service is available

**Steps:**
1. Send `GET /ping`.
2. Observe the response status code.
3. Observe the response body.

**Expected result:**
- Status code is `201 Created`.
- Endpoint is reachable.
- Response is returned without errors.

**Actual result:** _201 Created. Health check endpoint is reachable and returns 201 as expected._
**Status:** ✅ Pass

## TC-PING-002 — Negative: unsupported method on /ping

**Priority:** Medium  
**Type:** Negative / Method Validation

**Steps:**
1. Send `POST /ping`.
2. Observe the response status code and body.

**Expected result:**
- Request is rejected.
- API does not process unsupported method as a valid health check.

**Actual result:** _404 Not Found. POST is not supported on /ping; the API returns 404 rather than 405. Unsupported method is not processed._
**Status:** ✅ Pass


## TC-PING-003 — Negative: invalid path

**Priority:** Medium  
**Type:** Negative / Routing \
**Test data:** `/ping/abc`

**Steps:**
1. Send `GET /ping/abc`.
2. Observe the response status code.
3. Observe the response body.

**Expected result:**
- Status code is `404 Not Found`.
- Invalid path is not processed as a valid health check endpoint.

**Actual result:** _404 Not Found. Invalid path /ping/abc returns 404._
**Status:** ✅ Pass


## TC-PING-004 — Edge: repeated calls return stable result

**Priority:** Medium  
**Type:** Edge / Stability

**Steps:**
1. Send `GET /ping` several times.
2. Compare response status codes.
3. Observe whether response time remains acceptable.

**Expected result:**
- Endpoint consistently returns `201 Created`.
- No intermittent failures are observed.
- Response time remains acceptable.

**Actual result:** _201 Created on repeated calls. Endpoint is stable with no intermittent failures._
**Status:** ✅ Pass

---