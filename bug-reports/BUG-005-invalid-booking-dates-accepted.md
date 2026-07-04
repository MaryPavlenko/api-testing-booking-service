# BUG-005 — Invalid booking dates are accepted

**Severity:** Major  
**Priority:** Medium  
**Status:** Open  
**Environment:** Restful-Booker, Postman  
**Endpoints:** `POST /booking`, `PUT /booking/{id}`  
**Related test cases:** `TC-POST-011`, `TC-PUT-009`

## Description

The API accepts booking dates where the checkout date is earlier than the check-in date.

Bookings with an invalid date range are successfully created and updated instead of being rejected by business validation.

## Preconditions

- Restful-Booker API is available.
- Postman is used to send the requests.
- A valid booking exists for the `PUT` request.
- A valid authentication token is available for the `PUT` request.

## Steps to Reproduce

### Scenario 1 — Create booking

1. Send `POST /booking`.
2. Add header `Content-Type: application/json`.
3. Send the following request:

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

4. Observe the response.

### Scenario 2 — Update booking

1. Send `PUT /booking/{id}`.
2. Add a valid authentication token.
3. Add header `Content-Type: application/json`.
4. Send the same request body.
5. Observe the response.

## Expected Result

The API should validate the booking date range.

If `checkout` is earlier than `checkin`, the request should be rejected with an appropriate validation error (for example, `400 Bad Request`).

No booking should be created or updated using an invalid date range.

## Actual Result

Both endpoints accept the invalid booking dates.

- `POST /booking` successfully creates the booking.
- `PUT /booking/{id}` successfully updates the booking.

No validation error is returned.

## Impact

Invalid booking periods can be stored in the system, resulting in inconsistent or incorrect booking data.

Applications consuming the API may rely on the server to validate business rules and therefore store invalid records.

## Additional Notes

Both the booking creation and booking update endpoints exhibit the same behaviour, indicating that business validation for the booking date range is missing rather than implemented inconsistently.