# BUG-004 — Inconsistent validation of JSON field types

**Severity:** Major  
**Priority:** High  
**Status:** Open  
**Environment:** Restful-Booker, Postman  
**Endpoints:** `POST /booking`, `PATCH /booking/{id}`  
**Related test cases:** `TC-POST-006`, `TC-POST-007`, `TC-PATCH-007`

## Description

The API validates JSON field types inconsistently.

When an incorrect data type is provided:

- `totalprice` sent as a string is accepted, but the value is stored as `null`.
- `depositpaid` sent as a string is rejected with `400 Bad Request`.

The API applies different validation rules to similar input validation scenarios, resulting in inconsistent behaviour and potential data corruption.

## Preconditions

- Restful-Booker API is available.
- Postman is used to send the requests.
- A valid booking exists for `PATCH`.
- A valid authentication token is available for `PATCH`.

## Steps to Reproduce

### Scenario 1 — POST: totalprice as string

1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Send the following request:

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

4. Observe the response.

### Scenario 2 — PATCH: totalprice as string

1. Send `PATCH /booking/{id}`.
2. Add a valid authentication token.
3. Add header `Content-Type: application/json`.
4. Send:

```json
{ "totalprice": "111" }
```

5. Observe the response.

### Scenario 3 — POST: depositpaid as string

1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Send:

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

4. Observe the response.

## Expected Result

The API should validate JSON field types consistently.

For invalid field types, it should either:

- reject the request with a validation error (for example, `400 Bad Request`), or
- consistently convert supported values according to documented rules.

The API should not silently replace invalid values with `null`.

## Actual Result

### `totalprice` as string

- Request is accepted.
- `totalprice` is stored as `null`.

### `depositpaid` as string

- Request is rejected with `400 Bad Request`.

## Impact

Inconsistent type validation makes API behaviour unpredictable for clients.

Silently replacing invalid values with `null` may result in corrupted or incomplete booking data without informing the client that the submitted value was invalid.

## Additional Notes

The API validates `depositpaid` strictly but accepts an invalid type for `totalprice`, replacing the value with `null` instead of rejecting the request. Similar validation scenarios should be handled consistently across all fields.