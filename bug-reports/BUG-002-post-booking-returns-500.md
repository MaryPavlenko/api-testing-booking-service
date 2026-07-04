# BUG-002 — POST /booking returns 500 Internal Server Error

**Severity:** Major  
**Priority:** High  
**Status:** Open  
**Environment:** Restful-Booker, Postman  
**Endpoint:** `POST /booking`  
**Related test cases:** `TC-POST-004`, `TC-POST-005`

## Description

`POST /booking` returns `500 Internal Server Error` when the request body is empty or contains only partial booking data.

Invalid client input should be handled as a validation error rather than causing an internal server error.

## Preconditions

- Restful-Booker API is available.
- Postman is used to send the request.

## Steps to Reproduce

### Scenario 1 — Empty request body

1. Send `POST /booking`.
2. Add header:

```http
Content-Type: application/json
```

3. Send the following request body:

```json
{}
```

4. Observe the response.

---

### Scenario 2 — Missing required fields

1. Send `POST /booking`.
2. Add header:

```http
Content-Type: application/json
```

3. Send the following request body:

```json
{ "firstname": "Jim", "lastname": "Brown" }
```

4. Observe the response.

## Expected Result

- The request is rejected as invalid client input.
- The API returns an appropriate client error (for example, `400 Bad Request`).
- The response clearly indicates which required fields are missing or invalid.
- No booking is created.

## Actual Result

For both scenarios:

- Status code: `500 Internal Server Error`
- Response body:

```text
Internal Server Error
```

## Impact

Invalid client input triggers an internal server error instead of a validation error.

This makes it difficult for API clients to distinguish invalid requests from actual server failures and indicates that input validation is not handled correctly.

## Additional Notes

The same API handles invalid input differently across endpoints:

- `POST /booking` → `500 Internal Server Error`
- `PUT /booking/{id}` → `400 Bad Request`
- `PATCH /booking/{id}` → `200 OK`

This inconsistent behaviour suggests that request validation is implemented differently across CRUD operations.