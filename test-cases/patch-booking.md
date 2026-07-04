# PATCH /booking/{id} — Partial Update Booking Test Cases

**Endpoint:** `PATCH /booking/{id}`

**Purpose:** Verify that an existing booking can be partially updated with valid authorization and that unchanged fields remain intact.

- ✅ TC-PATCH-001 — Positive: valid token + one-field update → 200 OK
- ✅ TC-PATCH-002 — Positive: valid token + multiple-field update → 200 OK
- ✅ TC-PATCH-003 — Positive: after PATCH, GET returns updated values
- ✅ TC-PATCH-004 — Negative: no token / no Basic Auth → request rejected
- ✅ TC-PATCH-005 — Negative: invalid token
- ⚠️ TC-PATCH-006 — Negative: non-existent id
- ⚠️ TC-PATCH-007 — Negative: wrong field type in partial body
- ⚠️ TC-PATCH-008 — Edge: empty PATCH body
- ✅ TC-PATCH-009 — Edge: patch nested bookingdates only


## TC-PATCH-001 — Positive: valid token + one-field update → 200 OK

**Priority:** High  
**Type:** Positive / Functional

**Preconditions:**
- A valid auth token has been obtained
- A booking with a known id exists

**Test data:**
```json
{
  "firstname": "James"
}
```

**Steps:**
1. Send `PATCH /booking/{id}` with valid token.
2. Add header `Content-Type: application/json`.
3. Send request body above.
4. Observe response status code and body.

**Expected result:**
- Status code is `200 OK`.
- Only `firstname` is updated.
- Other fields remain unchanged.

**Actual result:** _200 OK. Sending only { "firstname": "Patched" } updated firstname while all other fields (lastname, totalprice, depositpaid, bookingdates, additionalneeds) remained unchanged. Partial update works correctly._
**Status:** ✅ Pass


## TC-PATCH-002 — Positive: valid token + multiple-field update → 200 OK

**Priority:** High  
**Type:** Positive / Functional

**Test data:**
```json
{
  "firstname": "James",
  "lastname": "Brown"
}
```

**Steps:**
1. Send `PATCH /booking/{id}` with valid token.
2. Add header `Content-Type: application/json`.
3. Send request body with multiple fields.
4. Observe the response status code and body.

**Expected result:**
- Status code is `200 OK`.
- Specified fields are updated successfully.
- Unchanged fields remain intact.

**Actual result:** _200 OK. Multiple fields updated successfully; fields not included in the request remained unchanged._
**Status:** ✅ Pass


## TC-PATCH-003 — Positive: after PATCH, GET returns updated values

**Priority:** High  
**Type:** Positive / Data Integrity

**Steps:**
1. Send `PATCH /booking/{id}` with valid token and partial body.
2. Send `GET /booking/{id}`.
3. Compare updated and unchanged fields.

**Expected result:**
- GET response contains updated values.
- Fields not included in PATCH remain unchanged.

**Actual result:** _200 OK. GET /booking/{id} after PATCH returns the updated values, and untouched fields are preserved — changes are persisted correctly._
**Status:** ✅ Pass


## TC-PATCH-004 — Negative: no token / no Basic Auth → request rejected

**Priority:** High  
**Type:** Negative / Authorisation

**Steps:**
1. Send `PATCH /booking/{id}` without token and without Basic Auth.
2. Send valid partial body.
3. Observe the response status code and body.

**Expected result:**
- Request is rejected.
- Booking data is not updated.
- Status code to be confirmed during execution.

**Actual result:** _403 Forbidden. PATCH without authorization is rejected; booking is not updated._
**Status:** ✅ Pass


## TC-PATCH-005 — Negative: invalid token

**Priority:** High  
**Type:** Negative / Authorization

**Steps:**
1. Send `PATCH /booking/{id}` with invalid token.
2. Send valid partial body.
3. Observe response status code and body.

**Expected result:**
- Request is rejected.
- Booking data is not updated.

**Actual result:** _403 Forbidden. Invalid token is rejected the same way as a missing token; booking is not updated._
**Status:** ✅ Pass


## TC-PATCH-006 — Negative: non-existent id

**Priority:** High  
**Type:** Negative / Data Integrity \
**Test data:** `/booking/999999999`

**Steps:**
1. Send `PATCH /booking/999999999` with valid token.
2. Send valid partial body.
3. Observe the response status code and body.

**Expected result:**
- Request is rejected.
- No new booking is created.
- Status code to be confirmed during execution.

**Actual result:** _403 Forbidden even with a valid token. PATCH appears to check authorization before resource existence._
**Status:** ⚠️ Needs clarification


## TC-PATCH-007 — Negative: wrong field type in partial body

**Priority:** Medium  
**Type:** Negative / Validation

**Test data:**
```json
{
  "totalprice": "111"
}
```

**Steps:**
1. Send `PATCH /booking/{id}` with valid token.
2. Add header `Content-Type: application/json`.
3. Send partial body where `totalprice` is a string.
4. Observe the response status code and body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- API handles incorrect data type consistently.

**Actual result:** _200 OK. totalprice sent as a non-numeric string was accepted and stored as null (invalid type not rejected, value lost). Similar to POST behavior._
**Status:** ⚠️ Needs clarification


## TC-PATCH-008 — Edge: empty PATCH body

**Priority:** Medium  
**Type:** Edge / Validation

**Test data:**
```json
{}
```

**Steps:**
1. Send `PATCH /booking/{id}` with valid token.
2. Add header `Content-Type: application/json`.
3. Send an empty JSON body.
4. Observe the response status code and body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- API handles empty PATCH body consistently.

**Actual result:** _200 OK. PATCH with an empty body {} is accepted and makes no changes. Note the inconsistency across endpoints for the same empty body: POST returns 500, PUT returns 400, PATCH returns 200._
**Status:** ⚠️ Needs clarification


## TC-PATCH-009 — Edge: patch nested bookingdates only

**Priority:** Medium  
**Type:** Edge / Partial Update

**Test data:**
```json
{
  "bookingdates": {
    "checkin": "2024-01-01",
    "checkout": "2024-01-05"
  }
}
```

**Steps:**
1. Send `PATCH /booking/{id}` with valid token.
2. Send partial body with `bookingdates` only.
3. Observe the response status code and body.
4. Send `GET /booking/{id}` and verify persisted values.

**Expected result:**
- Behaviour to be confirmed during execution.
- Booking dates are updated correctly.
- Other fields remain unchanged.

**Actual result:** _200 OK. Patching only the nested bookingdates object updated checkin/checkout while other fields remained intact._
**Status:** ✅ Pass

---