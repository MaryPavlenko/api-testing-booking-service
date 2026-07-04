# PUT /booking/{id} — Update Booking Test Cases

**Endpoint:** `PUT /booking/{id}`

**Purpose:** Verify that an existing booking can be fully updated with valid authorization and that invalid authorization, invalid data, and business edge cases are handled correctly.

- ✅ TC-PUT-001 — Positive: valid token + full valid body → 200 OK
- ✅ TC-PUT-002 — Positive: after PUT, GET /booking/{id} returns updated data
- ✅ TC-PUT-003 — Positive: valid Basic Auth + full valid body → 200 OK
- ✅ TC-PUT-004 — Negative: no token / no Basic Auth → request rejected
- ✅ TC-PUT-005 — Negative: invalid token → request rejected
- ✅ TC-PUT-006 — Negative: non-existent booking id
- ✅ TC-PUT-007 — Negative: empty body
- ⚠️ TC-PUT-008 — Negative: totalprice as string instead of number
- ❌ TC-PUT-009 — Edge: checkout date earlier than checkin
- ⚠️ TC-PUT-010 — Edge: firstname contains digits


## TC-PUT-001 — Positive: valid token + full valid body → 200 OK

**Priority:** High  
**Type:** Positive / Functional

**Preconditions:**
- A valid auth token has been obtained via `POST /auth`
- A booking with a known id exists

**Test data:**
```json
{
  "firstname": "James",
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
1. Send `PUT /booking/{id}` with header `Cookie: token=<token>`.
2. Add header `Content-Type: application/json`.
3. Send the request body above.
4. Observe the response status code.
5. Observe the response body.

**Expected result:**
- Status code is `200 OK`.
- Response body reflects the updated booking data.

**Actual result:** _200 OK. Booking updated successfully with a valid token. Response reflects the new values (firstname=Mary, lastname=Updated, totalprice=200). Automated tests confirm status and updated fields._
**Status:** ✅ Pass

## TC-PUT-002 — Positive: after PUT, GET /booking/{id} returns updated data

**Priority:** High  
**Type:** Positive / Data Integrity

**Preconditions:**
- A valid auth token has been obtained
- A booking with a known id exists

**Steps:**
1. Send `PUT /booking/{id}` with valid token and full valid body.
2. Send `GET /booking/{id}` for the same id.
3. Compare GET response body with the PUT request body.

**Expected result:**
- `PUT` returns `200 OK`.
- `GET /booking/{id}` returns the updated booking data.
- Updated values are persisted correctly.

**Actual result:** _200 OK. GET /booking/1615 returns all updated values (lastname=Updated, totalprice=200, depositpaid=false, new dates, additionalneeds=Dinner). PUT changes are persisted correctly._
**Status:** ✅ Pass


## TC-PUT-003 — Positive: valid Basic Auth + full valid body → 200 OK

**Priority:** Medium  
**Type:** Positive / Authorisation

**Steps:**
1. Send `PUT /booking/{id}` with valid Basic Auth.
2. Add header `Content-Type: application/json`.
3. Send a full valid booking body.
4. Observe the response status code and body.

**Expected result:**
- Status code is `200 OK`.
- Booking data is updated successfully.

**Actual result:** _200 OK. Booking updated successfully using Basic Auth (admin/password123) instead of a token. The alternative authorization method works for PUT, as documented._
**Status:** ✅ Pass


## TC-PUT-004 — Negative: no token / no Basic Auth → request rejected

**Priority:** High  
**Type:** Negative / Authorisation

**Steps:**
1. Send `PUT /booking/{id}` without token and without Basic Auth.
2. Send a full valid booking body.
3. Observe the response status code and body.

**Expected result:**
- Request is rejected.
- Booking data is not updated.
- Status code to be confirmed during execution.

**Actual result:** _403 Forbidden, booking not updated. Authorization enforced._  
**Status:** ✅ Pass


## TC-PUT-005 — Negative: invalid token → request rejected

**Priority:** High  
**Type:** Negative / Authorisation

**Steps:**
1. Send `PUT /booking/{id}` with invalid token.
2. Send a full valid booking body.
3. Observe the response status code and body.

**Expected result:**
- Request is rejected.
- Booking data is not updated.
- Status code to be confirmed during execution.

**Actual result:** _403 Forbidden, invalid token rejected._  
**Status:** ✅ Pass


## TC-PUT-006 — Negative: non-existent booking id

**Priority:** High  
**Type:** Negative / Data Integrity \ 
**Test data:** `/booking/999999999`

**Steps:**
1. Send `PUT /booking/999999999` with a valid token.
2. Send a full valid booking body.
3. Observe the response status code and body.

**Expected result:**
- Request is rejected.
- No new booking is created.
- Status code to be confirmed during execution.

**Actual result:** _405 Method Not Allowed. Updating a non-existent id returns 405 (valid token). Consistent with DELETE._  
**Status:** ✅ Pass


## TC-PUT-007 — Negative: empty body

**Priority:** High  
**Type:** Negative / Validation

**Test data:**
```json
{}
```

**Steps:**
1. Send `PUT /booking/{id}` with a valid token.
2. Add header `Content-Type: application/json`.
3. Send an empty JSON body.
4. Observe the response status code and body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- Booking data should not be updated with empty values.

**Actual result:** _400 Bad Request on empty body. Note: PUT correctly returns 400, while POST returns 500 for the same empty body — inconsistent validation between endpoints (PUT is correct here)._  
**Status:** ✅ Pass


## TC-PUT-008 — Negative: totalprice as string instead of number

**Priority:** Medium  
**Type:** Negative / Validation

**Test data:**
```json
{
  "firstname": "James",
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

**Steps:**
1. Send `PUT /booking/{id}` with a valid token.
2. Add header `Content-Type: application/json`.
3. Send request body where `totalprice` is a string.
4. Observe the response status code and body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- API handles incorrect data type consistently.

**Actual result:** _200 OK, totalprice sent as string "111" returned as number 111 (verify via GET). Note: PUT handles the string type differently from POST, which stored null — inconsistent type coercion between endpoints._  
**Status:** ⚠️ Needs clarification


## TC-PUT-009 — Edge: checkout date earlier than checkin

**Priority:** Medium  
**Type:** Edge / Business Logic

**Test data:**
```json
{
  "firstname": "James",
  "lastname": "Brown",
  "totalprice": 111,
  "depositpaid": true,
  "bookingdates": {
    "checkin": "2019-01-01",
    "checkout": "2018-01-01"
  },
  "additionalneeds": "Breakfast"
}
```

**Steps:**
1. Send `PUT /booking/{id}` with valid token.
2. Send request body where `checkout` is earlier than `checkin`.
3. Observe the response status code and body.

**Expected result:**
- Behaviour to be confirmed during execution.
- Checkout earlier than checkin should be reviewed as a potential business logic issue.

**Actual result:** _200 OK, booking updated with checkout earlier than checkin. No business logic validation — same as POST._  
**Status:** ❌ Fail


## TC-PUT-010 — Edge: firstname contains digits

**Priority:** Low  
**Type:** Edge / Input Validation

**Test data:**
```json
{
  "firstname": "James123",
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
1. Send `PUT /booking/{id}` with valid token.
2. Send request body where `firstname` contains digits.
3. Observe the response status code and body.

**Expected result:**
- Behaviour to be confirmed during execution.
- Name validation rules should be clarified.

**Actual result:** _200 OK, firstname "James123" accepted and stored. No validation on digits in name fields._  
**Status:** ⚠️ Needs clarification

---