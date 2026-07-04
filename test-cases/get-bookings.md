# GET /booking — Get Booking IDs Test Cases

**Endpoint:** `GET /booking`

**Purpose:** Verify that booking IDs can be retrieved and filtered by supported query parameters.

- ✅ TC-GET-BOOKINGS-001 — Positive: no filters → 200 OK and array of booking IDs
- ✅ TC-GET-BOOKINGS-002 — Positive: filter by firstname
- ✅ TC-GET-BOOKINGS-003 — Positive: filter by lastname
- ⚠️ TC-GET-BOOKINGS-004 — Positive: filter by checkin date
- ✅ TC-GET-BOOKINGS-005 — Positive: combined filters
- ⚠️TC-GET-BOOKINGS-006 — Negative: invalid date format in filter
- ✅ TC-GET-BOOKINGS-007 — Negative: unsupported query parameter
- ✅ TC-GET-BOOKINGS-008 — Edge: filters with no matches
- ✅ TC-GET-BOOKINGS-009 — Edge: repeated identical queries return consistent results


## TC-GET-BOOKINGS-001 — Positive: no filters → 200 OK and array of booking IDs

**Priority:** High  
**Type:** Positive / Functional

**Steps:**
1. Send `GET /booking`.
2. Observe the response status code.
3. Observe the response body structure.

**Expected result:**
- Status code is `200 OK`.
- Response body is an array.
- Each item contains `bookingid`.

**Actual result:** _200 OK, response is a non-empty array; each item contains bookingid._
**Status:** ✅ Pass


## TC-GET-BOOKINGS-002 — Positive: filter by firstname

**Priority:** Medium  
**Type:** Positive / Filtering \
**Test data:** `/booking?firstname=Jim`

**Steps:**
1. Send `GET /booking?firstname=Jim`.
2. Observe the response status code and body.

**Expected result:**
- Status code is `200 OK`.
- Response returns booking IDs matching the firstname filter.

**Actual result:** _200 OK. Filter firstname=John returned a non-empty array of IDs (14, 18, 22...). Verified via GET /booking/14 that firstname="John" — filter returns correct results._
**Status:** ✅ Pass


## TC-GET-BOOKINGS-003 — Positive: filter by lastname

**Priority:** Medium  
**Type:** Positive / Filtering \
**Test data:** `/booking?lastname=Brown`

**Expected result:**
- Status code is `200 OK`.
- Response returns booking IDs matching the lastname filter.

**Actual result:** _200 OK. Filter flastname=Smith returned a non-empty array of IDs (11, 17, 19...). Verified via GET /booking/14 that flastname=Smith" — filter returns correct results._
**Status:** ✅ Pass


## TC-GET-BOOKINGS-004 — Positive: filter by checkin date

**Priority:** Medium  
**Type:** Positive / Filtering \
**Test data:** `/booking?checkin=2018-01-01`

**Expected result:**
- Status code is `200 OK`.
- Response returns booking IDs matching the checkin filter.

**Actual result:** _Filter checkin returns 200 and a filtered subset (3 IDs for checkin=2030-01-01 vs a large list for 2018-01-01, confirming filtering is applied). However, results include a corrupted record (id=1220) with checkin="0NaN-aN-aN" and placeholder field values, which cannot satisfy the date condition. Filter passes corrupted dates through — see BUG report._
**Status:** ⚠️ Needs clarification


## TC-GET-BOOKINGS-005 — Positive: combined filters

**Priority:** Medium  
**Type:** Positive / Filtering \
**Test data:** `/booking?firstname=Jim&lastname=Brown`

**Expected result:**
- Status code is `200 OK`.
- Response returns a filtered subset matching all provided filters.

**Actual result:** _200 OK. Filter firstname=John&lastname=Smith returned a non-empty array of IDs. Verified via GET /booking/{id} that a returned booking has both firstname="John" and lastname="Smith" — combined filter works correctly._
**Status:** ✅ Pass


## TC-GET-BOOKINGS-006 — Negative: invalid date format in filter

**Priority:** Medium  
**Type:** Negative / Validation \
**Test data:** `/booking?checkin=01-01-2018`

**Expected result:**
- Behaviour/status to be confirmed during execution.
- API handles invalid date format consistently.

**Actual result:** _200 OK, returns a list of booking IDs. The invalid date format (01-01-2018 instead of YYYY-MM-DD) is not rejected — API appears to ignore the malformed date and return results as if no valid filter was applied. No validation error is returned._
**Status:** ⚠️ Needs clarification


## TC-GET-BOOKINGS-007 — Negative: unsupported query parameter

**Priority:** Low  
**Type:** Negative / Query Parameter Validation \
**Test data:** `/booking?unknown=value`

**Steps:**
1. Send `GET /booking?unknown=value`.
2. Observe the response status code.
3. Observe the response body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- Unsupported query parameter is either ignored or rejected consistently.

**Actual result:** _200 OK. The unsupported parameter (unknown=value) is ignored; the response returns the full list of booking IDs as if no filter was applied. This is acceptable REST behavior for unknown query parameters._
**Status:** ✅ Pass


## TC-GET-BOOKINGS-008 — Edge: filters with no matches

**Priority:** Medium  
**Type:** Edge / Filtering \
**Test data:** `/booking?firstname=NoSuchName`

**Steps:**
1. Send `GET /booking?firstname=NoSuchName`.
2. Observe the response status code.
3. Observe the response body.

**Expected result:**
- Status code is `200 OK`.
- Response body is an empty array.

**Actual result:** _200 OK, response body is an empty array []. No bookings match the non-existent name filter, and the API correctly returns an empty array rather than an error._
**Status:** ✅ Pass

## TC-GET-BOOKINGS-009 — Edge: repeated identical queries return consistent results

**Priority:** Low  
**Type:** Edge / Stability \
**Test data:** `/booking?firstname=Jim`

**Steps:**
1. Send `GET /booking?firstname=Jim`.
2. Send the same request again.
3. Compare response status codes and response body structure.

**Expected result:**
- Repeated identical queries return consistent status codes.
- Response structure remains stable.

**Actual result:** _200 OK on both requests. Two identical requests (firstname=John) returned the same status code and the same response structure — results are consistent across repeated calls._
**Status:** ✅ Pass

---