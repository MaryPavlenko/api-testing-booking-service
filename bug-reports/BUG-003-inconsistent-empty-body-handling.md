# BUG-003 — Inconsistent handling of empty request body across booking endpoints

**Severity:** Major  
**Priority:** High  
**Status:** Open  
**Environment:** Restful-Booker, Postman  
**Endpoints:** `POST /booking`, `PUT /booking/{id}`, `PATCH /booking/{id}`  
**Related test cases:** `TC-POST-004`, `TC-PUT-007`, `TC-PATCH-008`

## Description

The API handles an empty request body inconsistently across booking endpoints.

For the same invalid input (`{}`):

- `POST /booking` returns `500 Internal Server Error`
- `PUT /booking/{id}` returns `400 Bad Request`
- `PATCH /booking/{id}` returns `200 OK`

The inconsistent behaviour makes the API difficult to use and prevents clients from handling validation errors consistently.

## Preconditions

- Restful-Booker API is available.
- Postman is used to send the requests.
- A valid booking exists for `PUT` and `PATCH`.
- A valid authentication token is available for `PUT` and `PATCH`.

## Steps to Reproduce

### Scenario 1 — POST

1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Send an empty JSON body:

```json
{}
```

4. Observe the response.

### Scenario 2 — PUT

1. Send `PUT /booking/{id}`.
2. Add a valid authentication token.
3. Add header `Content-Type: application/json`.
4. Send the same empty JSON body:

```json
{}
```

5. Observe the response.

### Scenario 3 — PATCH

1. Send `PATCH /booking/{id}`.
2. Add a valid authentication token.
3. Add header `Content-Type: application/json`.
4. Send the same empty JSON body:

```json
{}
```

5. Observe the response.

## Expected Result

Booking endpoints should handle invalid empty request bodies consistently.

For the same invalid input, all endpoints should return the same type of validation error (for example, `400 Bad Request`) and should not process the request successfully.

## Actual Result

| Endpoint | Response |
|----------|----------|
| `POST /booking` | `500 Internal Server Error` |
| `PUT /booking/{id}` | `400 Bad Request` |
| `PATCH /booking/{id}` | `200 OK` |

## Impact

API consumers cannot rely on consistent validation behaviour across similar endpoints.

Different responses for the same invalid request increase implementation complexity and may lead to inconsistent error handling in client applications.

## Additional Notes

All three endpoints operate on the same booking resource and receive the same invalid input (`{}`), but each endpoint responds differently. This suggests inconsistent validation logic rather than intentional API behaviour.