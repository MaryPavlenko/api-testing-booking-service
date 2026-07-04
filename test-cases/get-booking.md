# GET /booking/{id} — Get Booking Test Cases

**Endpoint:** `GET /booking/{id}`

**Purpose:** Verify that a booking can be retrieved by id and that invalid or unavailable ids are handled correctly.

- ✅ TC-GET-BOOKING-001 — Positive: valid existing id → 200 OK
- ⚠️ TC-GET-BOOKING-002 — Positive: Accept application/xml → response body is XML
- ✅ TC-GET-BOOKING-003 — Negative: non-existent id
- ✅ TC-GET-BOOKING-004 — Negative: non-numeric id
- ✅ TC-GET-BOOKING-005 — Negative: invalid path format
- ✅ TC-GET-BOOKING-006 — Edge: response remains correct immediately after create
- ✅ TC-GET-BOOKING-007 — Edge: response for recently deleted id


## TC-GET-BOOKING-001 — Positive: valid existing id → 200 OK

**Priority:** High  
**Type:** Positive / Functional \
**Preconditions:** A booking with a known id exists

**Steps:**
1. Send `GET /booking/{id}`.
2. Add header `Accept: application/json`.
3. Observe the response status code and body.

**Expected result:**
- Status code is `200 OK`.
- Response contains booking details.
- Response includes `firstname`, `lastname`, `totalprice`, `depositpaid`, `bookingdates`, and `additionalneeds`.

**Actual result:** _200 OK. GET by valid id returns full booking with correct field types (verified by automated tests)._
**Status:** ✅ Pass


## TC-GET-BOOKING-002 — Positive: Accept application/xml → response body is XML

**Priority:** Medium  
**Type:** Positive / Contract \
**Preconditions:** A booking with a known id exists

**Steps:**
1. Send `GET /booking/{id}`.
2. Add header `Accept: application/xml`.
3. Observe the response format.

**Expected result:**
- Status code is `200 OK`.
- Response body is returned in XML format.

**Actual result:** _200 OK, but with Accept: application/xml the response Content-Type is text/html, not application/xml. GET /booking/{id} does not honor the Accept header for XML, unlike POST /booking which correctly returns XML. Inconsistent content negotiation between endpoints._
**Status:** ⚠️ Needs clarification


## TC-GET-BOOKING-003 — Negative: non-existent id

**Priority:** High  
**Type:** Negative / Data Availability \
**Test data:** `/booking/999999999`

**Steps:**
1. Send `GET /booking/999999999`.
2. Observe the response status code and body.

**Expected result:**
- Request returns not found response.
- Status/response body to be confirmed during execution.

**Actual result:** _404 Not Found. Non-existent id returns 404._  
**Status:** ✅ Pass


## TC-GET-BOOKING-004 — Negative: non-numeric id

**Priority:** Medium  
**Type:** Negative / Path Parameter Validation \
**Test data:** `/booking/abc`

**Expected result:**
- Request is rejected or returns not found.
- Status to be confirmed during execution.

**Actual result:** _404 Not Found. Non-numeric id (abc) returns 404._  
**Status:** ✅ Pass


## TC-GET-BOOKING-005 — Negative: invalid path format

**Priority:** Medium  
**Type:** Negative / Routing

**Test data:** `/booking/1/extra`

**Steps:**
1. Send `GET /booking/1/extra`.
2. Observe the response status code.
3. Observe the response body.

**Expected result:**
- Request is rejected or returns not found.
- Status code to be confirmed during execution.

**Actual result:** _404 Not Found. Invalid path returns 404._  
**Status:** ✅ Pass

## TC-GET-BOOKING-006 — Edge: response remains correct immediately after create

**Priority:** Medium  
**Type:** Edge / Data Integrity

**Steps:**
1. Create a new booking using `POST /booking`.
2. Save `bookingid`.
3. Send `GET /booking/{id}` using the saved id.
4. Compare the response body with created data.

**Expected result:**
- Newly created booking is available by id.
- Response data matches created booking data.

**Actual result:** _200 OK. Newly created booking is immediately retrievable with matching data._
**Status:** ✅ Pass


## TC-GET-BOOKING-007 — Edge: response for recently deleted id

**Priority:** Medium  
**Type:** Edge / Data Integrity

**Steps:**
1. Create a booking.
2. Delete the booking using `DELETE /booking/{id}` with valid authorization.
3. Send `GET /booking/{id}` for the deleted id.
4. Observe the response status code and body.

**Expected result:**
- Deleted booking is no longer available.
- Not found behaviour is confirmed.

**Actual result:** _After deleting booking id (DELETE returned 201), GET /booking/id returns 404 Not Found. The deleted booking is no longer retrievable — deletion is confirmed.
**Status:** ✅ Pass

---