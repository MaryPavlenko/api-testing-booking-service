# BUG-001 — POST /auth returns 200 OK for invalid authentication requests

**Severity:** Minor  
**Priority:** Medium  
**Status:** Open  
**Environment:** Restful-Booker, Postman  
**Endpoint:** `POST /auth`  
**Related test cases:** `TC-AUTH-003`, `TC-AUTH-005`, `TC-AUTH-006`, `TC-AUTH-007`

## Description

`POST /auth` returns `200 OK` for invalid authentication requests, including incorrect credentials, empty required fields, and missing request body.

Although the response body correctly indicates authentication failure (`"reason": "Bad credentials"`), the endpoint still returns a success HTTP status code instead of an authentication or client error status.

## Preconditions

- Restful-Booker API is available.
- Postman is used to send the request.

## Steps to Reproduce

### Scenario 1 — Invalid password

1. Send `POST /auth`.
2. Add header `Content-Type: application/json`.
3. Send the following request:

```json
{ "username": "admin", "password": "wrong-password" }
```

4. Observe the response.

### Scenario 2 — Empty username

```json
{ "username": "", "password": "password123" }
```

### Scenario 3 — Empty password

```json
{ "username": "admin", "password": "" }
```

### Scenario 4 — Missing request body

Send `POST /auth` without a request body.

## Expected Result

- Invalid authentication requests should not return a successful HTTP status.
- The API should return an appropriate client or authentication error (for example, `400 Bad Request` or `401 Unauthorized`).
- The response should clearly indicate that authentication failed.

## Actual Result

For all tested scenarios:

- Status code: `200 OK`
- Response body:

```json
{ "reason": "Bad credentials" }
```

## Impact

Returning `200 OK` for failed authentication requests may lead API clients to interpret unsuccessful authentication attempts as successful operations based solely on the HTTP status code.

## Additional Notes

The response body correctly reports `"Bad credentials"`, but the HTTP status code does not reflect the failed authentication outcome. Using an appropriate error status would make the API behaviour more consistent with common REST API practices.