# BUG-006 — POST /booking returns 200 OK instead of 201 Created

**Severity:** Minor  
**Priority:** Low  
**Status:** Open  
**Environment:** Restful-Booker, Postman  
**Endpoint:** `POST /booking`  
**Related test case:** `TC-POST-001`

## Description

The `POST /booking` endpoint successfully creates a new booking but returns `200 OK` instead of `201 Created`.

According to common REST API conventions, a successful resource creation request should return `201 Created`.

## Preconditions

- Restful-Booker API is available.
- Postman is used to send the request.

## Steps to Reproduce

1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Send a valid booking request:

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

4. Observe the response status code.
5. Verify that a new booking has been created.

## Expected Result

- A new booking is created successfully.
- The endpoint returns **201 Created**.
- The response contains the created booking information.

## Actual Result

- A new booking is created successfully.
- The endpoint returns **200 OK**.
- The response contains the created booking information.

## Impact

The booking is created successfully, so the functionality is not affected.

However, returning `200 OK` instead of `201 Created` makes the API less compliant with common REST conventions and may lead to inconsistent interpretation by API consumers and automated tools.

## Additional Notes

This issue affects the HTTP status code only. The booking is created successfully and the response body contains valid booking data.