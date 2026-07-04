# POST /booking — Create Booking Test Cases

**Endpoint:** `POST /booking`

**Purpose:** Verify that a booking can be created with valid data and that the API handles invalid request data, body formats, and boundary values correctly.

- ⚠️ TC-POST-001 — Positive: full valid JSON body → 200 OK
- ✅ TC-POST-002 — Positive: after POST, GET /booking/{id} returns booking
- ✅ TC-POST-003 — Positive: Accept application/xml → response body is XML
- ❌ TC-POST-004 — Negative: empty body
- ❌ TC-POST-005 — Negative: missing required fields
- ⚠️ TC-POST-006 — Negative: totalprice as string instead of number
- ⚠️ TC-POST-007 — Negative: depositpaid as string instead of boolean
- ❌ TC-POST-008 — Negative: malformed JSON
- ✅ TC-POST-009 — Edge: totalprice = 0
- ❌ TC-POST-010 — Edge: totalprice < 0


## TC-POST-001 — Positive: full valid JSON body → 200 OK

**Priority:** High  
**Type:** Positive / Functional

**Test data:**
```json
{
  "firstname": "Jim",
  "lastname": "Brown",
  "totalprice": 111,
  "depositpaid": true,
  "bookingdates": {
    "checkin": "2018-01-01",
    "checkout": "2019-01-01"
  },
  "additionalneeds": "Breakfast"
}
```

**Steps:**
1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Add header `Accept: application/json`.
4. Send the full valid body above.
5. Observe the response status code and body.

**Expected result:**
- Status code is `200 OK`.
- Response contains `bookingid`.
- Response echoes the created booking data.

**Actual result:** _200 OK. Response contains bookingid (568) and a nested "booking" object echoing the sent data (Mary Tester, correct fields). Note: API returns 200 OK instead of the semantically correct 201 Created for resource creation._
**Status:** ⚠️ Needs clarification


## TC-POST-002 — Positive: after POST, GET /booking/{id} returns created booking

**Priority:** High  
**Type:** Positive / Data Integrity

**Steps:**
1. Send `POST /booking` with full valid body.
2. Save `bookingid` from the response.
3. Send `GET /booking/{id}` using the saved id.
4. Compare returned data with the created booking.

**Expected result:**
- Created booking can be retrieved by id.
- Returned data matches the POST request body.

**Actual result:** _200 OK. GET /booking/568 returns the exact data created via POST (Mary Tester, totalprice 150, dates and additionalneeds all match). Created booking is persisted and retrievable._
**Status:** ✅ Pass


## TC-POST-003 — Positive: Accept application/xml → response body is XML

**Priority:** Medium  
**Type:** Positive / Contract

**Steps:**
1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Add header `Accept: application/xml`.
4. Send full valid booking body.
5. Observe response format.

**Expected result:**
- Status code is `200 OK`.
- Response body is returned in XML format.

**Actual result:** _200 OK. With Accept: application/xml header, the response body is returned in valid XML format (<created-booking>...</created-booking>). API correctly honors the Accept header for content negotiation._
**Status:** ✅ Pass


## TC-POST-004 — Negative: empty body

**Priority:** High  
**Type:** Negative / Validation

**Test data:**
```json
{}
```

**Steps:**
1. Send `POST /booking` with an empty JSON body.
2. Observe the response status code and body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- Booking should not be created with empty values.

**Actual result:** _Returns 500 Internal Server Error instead of 400 Bad Request; API does not validate empty/incomplete body and crashes with unhandled exception._
**Status:** ❌ Fail


## TC-POST-005 — Negative: missing required fields

**Priority:** High  
**Type:** Negative / Validation

**Test data:**
```json
{
  "firstname": "Jim",
  "lastname": "Brown"
}
```

**Steps:**
1. Send `POST /booking` with missing required fields.
2. Observe the response status code and body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- Booking should not be created without required fields.

**Actual result:** _Returns 500 Internal Server Error instead of 400 Bad Request; API does not validate empty/incomplete body and crashes with unhandled exception._
**Status:** ❌ Fail


## TC-POST-006 — Negative: totalprice as string instead of number

**Priority:** Medium  
**Type:** Negative / Validation

**Test data:**
```json
{
  "firstname": "Jim",
  "lastname": "Brown",
  "totalprice": "111",
  "depositpaid": true,
  "bookingdates": {
    "checkin": "2018-01-01",
    "checkout": "2019-01-01"
  },
  "additionalneeds": "Breakfast"
}
```

**Expected result:**
- Behaviour/status to be confirmed during execution.
- API handles incorrect data type consistently.

**Actual result:** _200 OK, booking created but totalprice silently set to null; invalid type accepted instead of rejected with 400. Silent data loss._
**Status:** ⚠️ Needs clarification


## TC-POST-007 — Negative: depositpaid as string instead of boolean

**Priority:** Medium  
**Type:** Negative / Validation

**Test data:**
```json
{
  "firstname": "Jim",
  "lastname": "Brown",
  "totalprice": 111,
  "depositpaid": "true",
  "bookingdates": {
    "checkin": "2018-01-01",
    "checkout": "2019-01-01"
  },
  "additionalneeds": "Breakfast"
}
```

**Steps:**
1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Send request body where `depositpaid` is a string.
4. Observe the response status code and body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- API handles incorrect data type consistently.

**Actual result:** _400 Bad Request. depositpaid as string "true" is rejected. Note inconsistency: totalprice as a string is accepted (200, stored as null), but depositpaid as a string is rejected (400). API validates data types inconsistently across fields._
**Status:** ⚠️ Needs clarification


## TC-POST-008 — Negative: malformed JSON

**Priority:** High  
**Type:** Negative / Contract

**Test data:**
```json
{
  "firstname": "Jim",
  "lastname": "Brown",
  "totalprice": 111,
```

**Steps:**
1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Send malformed JSON body.
4. Observe the response status code and body.

**Expected result:**
- Request is rejected.
- Booking is not created.
- Status code to be confirmed during execution.

**Actual result:** _200 OK, booking created (id 1996) with checkout (2025-06-01) earlier than checkin (2025-06-10). Confirmed via GET /booking/1996 that the invalid booking is persisted and retrievable. No business logic validation on dates._
**Status:** ❌ Fail


## TC-POST-009 — Edge: totalprice = 0

**Priority:** Medium  
**Type:** Edge / Boundary

**Test data:**
```json
{
  "firstname": "Jim",
  "lastname": "Brown",
  "totalprice": 0,
  "depositpaid": true,
  "bookingdates": {
    "checkin": "2018-01-01",
    "checkout": "2019-01-01"
  },
  "additionalneeds": "Breakfast"
}
```

**Steps:**
1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Send request body with `totalprice` equal to `0`.
4. Observe the response status code and body.

**Expected result:**
- Behaviour to be confirmed during execution.
- Free booking behaviour should be reviewed as a business rule.

**Actual result:** _200 OK, booking created with totalprice 0. Zero price is accepted. Whether free bookings are valid is a business rule question, not necessarily a defect._
**Status:** ✅ Pass


## TC-POST-010 — Edge: totalprice < 0

**Priority:** Medium  
**Type:** Edge / Boundary / Business Logic

**Test data:**
```json
{
  "firstname": "Jim",
  "lastname": "Brown",
  "totalprice": -1,
  "depositpaid": true,
  "bookingdates": {
    "checkin": "2018-01-01",
    "checkout": "2019-01-01"
  },
  "additionalneeds": "Breakfast"
}
```

**Steps:**
1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Send request body with negative `totalprice`.
4. Observe the response status code and body.

**Expected result:**
- Behaviour to be confirmed during execution.
- Negative price should be reviewed as a potential business logic issue.

**Actual result:** _200 OK, booking created with totalprice -1. Negative price is accepted without validation. Confirmed via GET that the booking persists with a negative price — business logic defect._
**Status:** ❌ Fail

---