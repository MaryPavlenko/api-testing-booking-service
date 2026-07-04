# OBS-002 — Invalid filter behaviour

**Status:** Observation  
**Environment:** Restful-Booker, Postman  
**Endpoint:** `GET /booking`  
**Related test cases:** `TC-GET-BOOKINGS-004`, `TC-GET-BOOKINGS-006`

## Summary

The `GET /booking` endpoint does not consistently validate the `checkin` query parameter.

When testing both valid and invalid date filters, the endpoint returned results that included unexpected or invalid date values.

The expected behaviour for invalid date formats is not described in the API documentation.

## Test Scenarios

### Scenario 1 — Valid date filter

**Request** ```http GET /booking?checkin=2018-01-01 ```

**Observed behaviour**

- The request completed successfully.
- Returned data included unexpected date values (for example, `0NaN-aN-aN`) in some booking records.

---

### Scenario 2 — Invalid date format

**Request** ```http GET /booking?checkin=01-01-2018 ```

**Observed behaviour**

- The request was processed successfully.
- The API did not reject the invalid date format.
- Returned results did not clearly indicate that the filter value was invalid.

## Expected Behaviour

The API documentation should clearly define how invalid date filters are handled.

Possible expected behaviours include:

- returning `400 Bad Request`;
- returning an empty result set;
- ignoring the invalid filter.

The behaviour should be predictable and documented.

## Notes

This observation highlights ambiguous filter validation rather than a confirmed defect.

Further clarification from the API documentation or maintainers would be required before classifying this behaviour as a bug.