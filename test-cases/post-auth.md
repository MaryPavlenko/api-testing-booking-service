# POST /auth — Create Token Test Cases

**Endpoint:** `POST /auth`

**Purpose:** Verify that the API creates an auth token for valid credentials and handles invalid authentication data correctly.

- ✅ TC-AUTH-001 — Positive: valid credentials → token returned
- TC-AUTH-002 — Positive: repeated valid login requests produce usable tokens
- ⚠️ TC-AUTH-003 — Negative: invalid password
- ✅ TC-AUTH-004 — Negative: invalid username
- ⚠️ TC-AUTH-005 — Negative: empty username
- ⚠️ TC-AUTH-006 — Negative: empty password
- ⚠️ TC-AUTH-007 — Negative: missing request body
- ✅ TC-AUTH-008 — Negative: malformed JSON
- ✅ TC-AUTH-009 — Edge: extra unexpected field in body

## TC-AUTH-001 — Positive: valid credentials → token returned

**Priority:** High  
**Type:** Positive / Authentication

**Test data:**
```json
{
  "username": "admin",
  "password": "password123"
}
```

**Steps:**
1. Send `POST /auth`.
2. Add header `Content-Type: application/json`.
3. Send valid username and password.
4. Observe the response status code.
5. Observe the response body.

**Expected result:**
- Status code is `200 OK`.
- Response body contains a non-empty `token` field.
- Token can be used for authenticated endpoints.

**Actual result:** 200 OK, token returned (e.g. d0224058816160d)
**Status:** ✅ Pass


## TC-AUTH-002 — Positive: repeated valid login requests produce usable tokens

**Priority:** Medium  
**Type:** Positive / Authentication

**Test data:**
```json
{
  "username": "admin",
  "password": "password123"
}
```

**Steps:**
1. Send `POST /auth` with valid credentials.
2. Save the returned token.
3. Send `POST /auth` again with the same valid credentials.
4. Save the second returned token.
5. Use one returned token for an authenticated request.

**Expected result:**
- Both requests return `200 OK`.
- Both responses contain non-empty token values.
- Returned token can be used for authenticated requests.

**Actual result:** _To be filled during execution._  
**Status:** ✅ Pass / ❌ Fail / ⚠️ Needs clarification


## TC-AUTH-003 — Negative: invalid password

**Priority:** High  
**Type:** Negative / Authentication

**Test data:**
```json
{
  "username": "admin",
  "password": "wrong-password"
}
```

**Steps:**
1. Send `POST /auth`.
2. Add header `Content-Type: application/json`.
3. Send valid username and invalid password.
4. Observe the response status code.
5. Observe the response body.

**Expected result:**
- Authentication fails.
- Token is not returned.
- Status code and response message to be confirmed during execution.

**Actual result:** 200 OK, body: {"reason":"Bad credentials"}, token not returned. API returns 200 instead of expected 401 for invalid credentials.
**Status:** ⚠️ Needs clarification


## TC-AUTH-004 — Negative: invalid username

**Priority:** High  
**Type:** Negative / Authentication

**Test data:**
```json
{
  "username": "wrong-user",
  "password": "password123"
}
```

**Steps:**
1. Send `POST /auth`.
2. Add header `Content-Type: application/json`.
3. Send invalid username and valid password.
4. Observe the response status code.
5. Observe the response body.

**Expected result:**
- Authentication fails.
- Token is not returned.
- Status code and response message to be confirmed during execution.

**Actual result:** _200 OK, body: {"reason":"Bad credentials"}, token not returned_  
**Status:** ✅ Pass


## TC-AUTH-005 — Negative: empty username

**Priority:** Medium  
**Type:** Negative / Validation

**Test data:**
```json
{
  "username": "",
  "password": "password123"
}
```

**Steps:**
1. Send `POST /auth`.
2. Add header `Content-Type: application/json`.
3. Send empty username and valid password.
4. Observe the response status code.
5. Observe the response body.

**Expected result:**
- Authentication fails.
- Token is not returned.
- Status code and response message to be confirmed during execution.

**Actual result:** _200 OK {"reason": "Bad credentials"}_  
**Status:** ⚠️ Needs clarification


## TC-AUTH-006 — Negative: empty password

**Priority:** Medium  
**Type:** Negative / Validation

**Test data:**
```json
{
  "username": "admin",
  "password": ""
}
```

**Steps:**
1. Send `POST /auth`.
2. Add header `Content-Type: application/json`.
3. Send valid username and empty password.
4. Observe the response status code.
5. Observe the response body.

**Expected result:**
- Authentication fails.
- Token is not returned.
- Status code and response message to be confirmed during execution.

**Actual result:** _200 OK {"reason": "Bad credentials"}_  
**Status:** ⚠️ Needs clarification


## TC-AUTH-007 — Negative: missing request body

**Priority:** High  
**Type:** Negative / Validation

**Steps:**
1. Send `POST /auth`.
2. Add header `Content-Type: application/json`.
3. Do not send a request body.
4. Observe the response status code.
5. Observe the response body.

**Expected result:**
- Request is rejected or authentication fails.
- Token is not returned.
- Status code and response message to be confirmed during execution.

**Actual result:** _200 OK, body: {"reason":"Bad credentials"}, token not returned. Missing body is treated as invalid credentials rather than a bad request. API returns 200 instead of 400/401._
**Status:** ⚠️ Needs clarification


## TC-AUTH-008 — Negative: malformed JSON

**Priority:** High  
**Type:** Negative / Contract

**Test data:**
```json
{
  "username": "admin",
  "password": "password123"
```

**Steps:**
1. Send `POST /auth`.
2. Add header `Content-Type: application/json`.
3. Send malformed JSON body.
4. Observe the response status code.
5. Observe the response body.

**Expected result:**
- Request is rejected.
- Token is not returned.
- Status code to be confirmed during execution.

**Actual result:** _400 Bad Request, body: "Bad Request". Malformed JSON is correctly rejected._
**Status:** ✅ Pass


## TC-AUTH-009 — Edge: extra unexpected field in body

**Priority:** Low  
**Type:** Edge / Contract

**Test data:**
```json
{
  "username": "admin",
  "password": "password123",
  "role": "admin"
}
```

**Steps:**
1. Send `POST /auth`.
2. Add header `Content-Type: application/json`.
3. Send valid credentials with an extra unexpected field.
4. Observe the response status code.
5. Observe the response body.

**Expected result:**
- Behaviour to be confirmed during execution.
- API handles unexpected fields consistently.
- Token handling remains secure and predictable.

**Actual result:** _200 OK, token returned. Extra field "role" is ignored, request processed normally._
**Status:** ✅ Pass

---