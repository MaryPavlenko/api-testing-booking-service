# API Testing Checklist — Restful-Booker

Coverage map for the Restful-Booker API.
Each endpoint is checked across positive, negative, and edge/boundary categories. Detailed steps live in the test-cases folder.

1. GET /ping — Health check
2. POST /auth — Create token
3. POST /booking — Create booking
4. GET /booking — Get booking IDs
5. GET /booking/{id} — Get booking
6. PUT /booking/{id} — Update booking (auth required)
7. PATCH /booking/{id} — Partial update booking (auth required)
8. DELETE /booking/{id} — Delete booking (auth required)

## 1️⃣ GET /ping — Health check
### ✅ Positive
- [ ] GET /ping → 201 Created.
- [ ] Response time is acceptable and endpoint is consistently reachable.
- [ ] Response body format is valid for the health check endpoint.

### ❌ Negative
- [ ] Unsupported HTTP method on /ping (e.g. POST/PUT/DELETE) → rejected.
- [ ] Invalid path (e.g. /ping/abc) → 404 Not Found.

### ⚠️ Edge / boundary
- [ ] Repeated calls to /ping return stable status and do not degrade.
- [ ] Verify response under minimal headers / no custom headers.

## 2️⃣ POST /auth — Create token
### ✅ Positive
- [ ] Valid username and password → 200 OK and token is returned.
- [ ] Response contains token field with non-empty value.
- [ ] Content-Type: application/json is accepted.

### ❌ Negative
- [ ] Invalid password → auth failure response (to be confirmed during execution).
- [ ] Invalid username → auth failure response (to be confirmed during execution).
- [ ] Empty username → auth failure response.
- [ ] Empty password → auth failure response.
- [ ] Missing request body → rejected / auth failure.
- [ ] Wrong field types (e.g. numeric username/password) → behavior/status to be confirmed.
- [ ] Malformed JSON → request rejected.

### ⚠️ Edge / boundary
- [ ] Very long username/password values → behavior to be confirmed.
- [ ] Special characters in username/password → behavior to be confirmed.
- [ ] Extra unexpected fields in body → behavior to be confirmed.
- [ ] Repeated valid login requests produce usable tokens.

## 3️⃣ POST /booking — Create booking
### ✅ Positive
- [ ] Full valid JSON body → 200 OK, bookingid returned, response echoes created booking.
- [ ] Full valid XML body → 200 OK, booking created.
- [ ] Accept: application/json → response body is JSON.
- [ ] Accept: application/xml → response body is XML.
- [ ] After POST, GET /booking/{id} returns the created booking.

### ❌ Negative
- [ ] Empty body → rejected / behavior to be confirmed.
- [ ] Missing required fields → behavior/status to be confirmed.
- [ ] totalprice as string instead of number → behavior/status to be confirmed.
- [ ] depositpaid as string instead of boolean → behavior/status to be confirmed.
- [ ] Malformed JSON/XML body → request rejected.

### ⚠️ Edge / boundary
- [ ] totalprice = 0 → behavior to be confirmed.
- [ ] totalprice < 0 → behavior to be confirmed.
- [ ] totalprice very large value → behavior to be confirmed.
- [ ] checkout earlier than checkin → behavior to be confirmed.
- [ ] firstname/lastname with special characters → behavior to be confirmed.
- [ ] Very long firstname/lastname values → behavior to be confirmed.
- [ ] additionalneeds empty string → behavior to be confirmed.

## 4️⃣ GET /booking — Get booking IDs
### ✅ Positive
- [ ] No filters → 200 OK and response is an array of bookingid objects.
- [ ] Filter by firstname → only matching booking IDs are returned.
- [ ] Filter by lastname → only matching booking IDs are returned.
- [ ] Filter by checkin → only matching booking IDs are returned.
- [ ] Filter by checkout → only matching booking IDs are returned.
- [ ] Combined filters (e.g. firstname + lastname, checkin + checkout) → filtered subset is returned.

### ❌ Negative
- [ ] Invalid date format in checkin/checkout → behavior/status to be confirmed.
- [ ] Unsupported query parameter is ignored or rejected (to be confirmed).
- [ ] Invalid path (e.g. /booking/abc) → 404 Not Found.

### ⚠️ Edge / boundary
- [ ] Filters with no matches → empty array.
- [ ] Special characters in firstname/lastname filter → behavior to be confirmed.
- [ ] Very long filter values → behavior to be confirmed.
- [ ] Repeated identical queries return consistent results.

## 5️⃣ GET /booking/{id} — Get booking
### ✅ Positive
- [ ] Valid existing id → 200 OK and response contains the expected booking structure.
- [ ] Accept: application/json → response body is JSON.
- [ ] Accept: application/xml → response body is XML.
- [ ] Response includes firstname, lastname, totalprice, depositpaid, bookingdates, additionalneeds.

### ❌ Negative
- [ ] Non-existent id → 404 Not Found or empty/not found response (to be confirmed).
- [ ] Non-numeric id (e.g. /booking/abc) → request rejected / not found (to be confirmed).
- [ ] Invalid path format → 404 Not Found.

### ⚠️ Edge / boundary
- [ ] Smallest existing id → booking is returned correctly.
- [ ] Largest existing id from current dataset → booking is returned correctly.
- [ ] Response remains correct immediately after create/update operations.
- [ ] Response for recently deleted id → not found behavior confirmed.

## 6️⃣ PUT /booking/{id} — Update booking (auth required)
### ✅ Positive
- [ ] Valid token + full valid body → 200 OK, response reflects updated data.
- [ ] Valid Basic Auth instead of token → 200 OK.
- [ ] After PUT, GET /booking/{id} returns the updated data.
- [ ] Send JSON body (Content-Type: application/json) → 200 OK, booking updated.
- [ ] Send XML body (Content-Type: text/xml) → 200 OK, booking updated.
- [ ] Accept: application/json → response body is JSON.
- [ ] Accept: application/xml → response body is XML.

### ❌ Negative
- [ ] No token → request rejected (status to be confirmed).
- [ ] Non-numeric id (e.g. /booking/abc) → status to be confirmed.
- [ ] Invalid/expired token → request rejected (to be confirmed).
- [ ] Non-existent id → status to be confirmed.
- [ ] Empty body → to be confirmed.
- [ ] totalprice as string instead of number → to be confirmed.
- [ ] depositpaid as string instead of boolean → status/behavior to be confirmed.
- [ ] Malformed JSON/XML body → request rejected.

### ⚠️ Edge / boundary
- [ ] Incomplete body (missing lastname) → behavior to be confirmed.
- [ ] totalprice = 0 → behavior to be confirmed.
- [ ] totalprice < 0 → behavior to be confirmed.
- [ ] totalprice very large value → behavior to be confirmed.
- [ ] checkout date earlier than checkin date → to be confirmed.
- [ ] firstname containing special characters → to be confirmed.
- [ ] firstname containing a very long value → to be confirmed.
- [ ] lastname containing special characters → to be confirmed.
- [ ] lastname containing a very long value → to be confirmed.
- [ ] firstname containing digits (e.g. "James123") → to be confirmed.
- [ ] lastname containing digits → behavior to be confirmed.

## 7️⃣ PATCH /booking/{id} — Partial update booking (auth required)
### ✅ Positive
- [ ] Valid token + one-field update → 200 OK, only specified field changes.
- [ ] Valid token + multiple-field update → 200 OK.
- [ ] Valid Basic Auth instead of token → 200 OK.
- [ ] After PATCH, GET /booking/{id} returns updated values.
- [ ] Unchanged fields remain intact after partial update.

### ❌ Negative
- [ ] No token / no Basic Auth → request rejected (expected: 403 to be confirmed).
- [ ] Invalid token → request rejected (expected: 403 to be confirmed).
- [ ] Non-existent id → status to be confirmed.
- [ ] Non-numeric id → status to be confirmed.
- [ ] Wrong field types in partial body → behavior/status to be confirmed.
- [ ] Malformed JSON/XML body → request rejected.

### ⚠️ Edge / boundary
- [ ] Empty PATCH body → behavior/status to be confirmed.
- [ ] Patch nested bookingdates only → behavior to be confirmed.
- [ ] totalprice = 0 / negative / very large value → behavior to be confirmed.
- [ ] Special characters or long values in updated string fields → behavior to be confirmed.

## 8️⃣ DELETE /booking/{id} — Delete booking (auth required)
### ✅ Positive
- [ ] Valid token + existing id → 201 Created.
- [ ] Valid Basic Auth + existing id → 201 Created.
- [ ] After DELETE, GET /booking/{id} confirms the booking is no longer available.

### ❌ Negative
- [ ] No token / no Basic Auth → request rejected (expected: 403 to be confirmed).
- [ ] Invalid token → request rejected (expected: 403 to be confirmed).
- [ ] Non-existent id → status to be confirmed.
- [ ] Non-numeric id (e.g. /booking/abc) → status to be confirmed.

### ⚠️ Edge / boundary
- [ ] Delete the same booking twice → second response/status to be confirmed.
- [ ] Delete recently created booking → deletion succeeds.
- [ ] Delete recently updated booking → deletion succeeds.