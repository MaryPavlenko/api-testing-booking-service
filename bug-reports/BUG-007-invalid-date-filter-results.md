# BUG-007 — Invalid date filter returns corrupted booking data

**Severity:** Major  
**Priority:** Medium  
**Status:** Open  
**Environment:** Restful-Booker, Postman  
**Endpoint:** `GET /booking`  
**Related test cases:** `TC-GET-BOOKINGS-004`, `TC-GET-BOOKINGS-006`

## Description

The `GET /booking` endpoint returns booking records with invalid date values when filtering by the `checkin` parameter.

Instead of returning only valid matching bookings or rejecting an invalid date format, the API includes records containing corrupted date values such as `0NaN-aN-aN`.

This indicates that invalid or corrupted booking data can be returned by the endpoint.

## Preconditions

- Restful-Booker API is available.
- Postman is used to send the requests.

## Steps to Reproduce

### Scenario 1 — Valid date filter

1. Send the following request:

```
GET /booking?checkin=2018-01-01
```

2. Observe the response.

### Scenario 2 — Invalid date format

1. Send the following request:

```
GET /booking?checkin=01-01-2018
```

2. Observe the response.

## Expected Result

For a valid date filter:

- Only bookings matching the specified date should be returned.
- Returned booking data should contain valid date values.

For an invalid date format:

- The API should reject the request with a validation error (for example, `400 Bad Request`) or return an empty result set.
- Corrupted or invalid booking data should never be returned.

## Actual Result

- The endpoint returns booking records containing invalid date values such as:

```
0NaN-aN-aN
```

- Invalid date values appear in the response instead of valid booking dates.
- The API does not consistently validate the date filter format.

## Impact

API consumers may receive corrupted booking data when filtering by dates.

Invalid values can lead to parsing errors, incorrect reporting, or unexpected behaviour in client applications.

## Additional Notes

The issue was observed while testing both a valid `checkin` filter and an invalid date format.

The presence of corrupted date values (`0NaN-aN-aN`) suggests that the API does not properly validate or sanitize date values before returning the response.