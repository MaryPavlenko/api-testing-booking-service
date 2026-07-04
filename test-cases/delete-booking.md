# DELETE /booking/{id} — Delete Booking Test Cases

**Endpoint:** `DELETE /booking/{id}`

**Purpose:** Verify that an existing booking can be deleted with valid authorization and that invalid authorization or invalid booking ids are handled correctly.

- ✅ TC-DELETE-001 — Positive: valid token + existing id → 201 Created
- ✅ TC-DELETE-002 — Positive: after DELETE, GET /booking/{id} confirms booking is unavailable
- ✅ TC-DELETE-003 — Positive: valid Basic Auth + existing id
- ✅ TC-DELETE-004 — Negative: no token / no Basic Auth
- ✅ TC-DELETE-005 — Negative: invalid token
- ✅ TC-DELETE-006 — Negative: non-existent id
- ✅ TC-DELETE-007 — Negative: non-numeric id
- ✅ TC-DELETE-008 — Edge: delete same booking twice

## TC-DELETE-001 — Positive: valid token + existing id → 201 Created

**Priority:** High  
**Type:** Positive / Functional

**Preconditions:**
- A valid auth token has been obtained
- A booking with a known id exists

**Steps:**
1. Send `DELETE /booking/{id}` with header `Cookie: token=<token>`.
2. Observe response status code and body.

**Expected result:**
- Status code is `201 Created`.
- Booking is deleted successfully.

**Actual result:** _201 Created. Booking deleted successfully with a valid token (Cookie: token={{token}}). Note: API returns 201 Created for a delete operation, as stated in the docs._
**Status:** ✅ Pass


## TC-DELETE-002 — Positive: after DELETE, GET /booking/{id} confirms booking is unavailable

**Priority:** High  
**Type:** Positive / Data Integrity

**Steps:**
1. Create a booking.
2. Delete the booking with valid authorization.
3. Send `GET /booking/{id}` for the deleted booking.
4. Observe response status code and body.

**Expected result:**
- Deleted booking is no longer available.
- Not found behaviour is confirmed.

**Actual result:** _404 Not Found. After DELETE, GET /booking/{id} returns 404 — the booking is confirmed removed from the system. Full lifecycle (create → delete → verify gone) works correctly._
**Status:** ✅ Pass


## TC-DELETE-003 — Positive: valid Basic Auth + existing id

**Priority:** Medium  
**Type:** Positive / Authorization

**Preconditions:**
- A booking with a known id exists
- Valid Basic Auth credentials are available

**Steps:**
1. Send `DELETE /booking/{id}` with valid Basic Auth.
2. Observe the response status code.
3. Observe the response body.

**Expected result:**
- Status code is `201 Created`.
- Booking is deleted successfully.

**Actual result:** _201 Created. Booking deleted successfully using Basic Auth (admin/password123) instead of a token. The alternative authorization method works as documented._
**Status:** ✅ Pass


## TC-DELETE-004 — Negative: no token / no Basic Auth

**Priority:** High  
**Type:** Negative / Authorization

**Steps:**
1. Send `DELETE /booking/{id}` without token and without Basic Auth.
2. Observe the response status code and body.
3. Send `GET /booking/{id}` to verify whether the booking still exists.

**Expected result:**
- Request is rejected.
- Booking is not deleted.
- Status code to be confirmed during execution.

**Actual result:** _403 Forbidden, body: "Forbidden". Booking is not deleted. Authorization is correctly enforced — DELETE without a token is rejected._
**Status:** ✅ Pass


## TC-DELETE-005 — Negative: invalid token

**Priority:** High  
**Type:** Negative / Authorization

**Steps:**
1. Send `DELETE /booking/{id}` with invalid token.
2. Observe the response status code and body.
3. Send `GET /booking/{id}` to verify whether the booking still exists.

**Expected result:**
- Request is rejected.
- Booking is not deleted.
- Status code to be confirmed during execution.

**Actual result:** _403 Forbidden, body: "Forbidden". Booking is not deleted. An invalid token is rejected the same way as a missing token — authorization is correctly enforced._
**Status:** ✅ Pass


## TC-DELETE-006 — Negative: non-existent id

**Priority:** Medium  
**Type:** Negative / Data Availability \
**Test data:** `/booking/999999999`

**Steps:**
1. Send `DELETE /booking/999999999` with valid authorisation.
2. Observe the response status code.
3. Observe the response body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- API handles deletion of non-existing booking consistently.

**Actual result:** _405 Method Not Allowed (with a valid token). Deleting a non-existent booking id (999999999) returns 405, consistent with the double-delete behavior._
**Status:** ✅ Pass


## TC-DELETE-007 — Negative: non-numeric id

**Priority:** Medium  
**Type:** Negative / Path Parameter Validation

**Test data:** `/booking/abc`

**Steps:**
1. Send `DELETE /booking/abc` with valid authorisation.
2. Observe the response status code.
3. Observe the response body.

**Expected result:**
- Behaviour/status to be confirmed during execution.
- API handles non-numeric id consistently.

**Actual result:** _405 Method Not Allowed (with a valid token). Deleting a non-numeric id (abc) returns 405._
**Status:** ✅ Pass


## TC-DELETE-008 — Edge: delete same booking twice

**Priority:** Medium  
**Type:** Edge / Idempotency

**Steps:**
1. Create a booking.
2. Delete the booking with valid authorisation.
3. Send `DELETE /booking/{id}` for the same id again.
4. Observe the second response status code and body.

**Expected result:**
- First DELETE succeeds.
- Second DELETE behaviour/status is confirmed.
- API handles repeated delete request consistently.

**Actual result:** _405 Method Not Allowed. First DELETE succeeds (201); second DELETE of the same id returns 405 — API handles repeated deletion of an already-removed booking._
**Status:** ✅ Pass

---