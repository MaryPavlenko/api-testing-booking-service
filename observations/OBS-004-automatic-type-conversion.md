# OBS-004 — Automatic type conversion behaviour

**Status:** Observation  
**Environment:** Restful-Booker, Postman  
**Endpoints:** `POST /booking`, `PATCH /booking/{id}`  
**Related test cases:** `TC-POST-006`, `TC-POST-007`, `TC-PATCH-007`

## Summary

The API handles incorrect JSON field types inconsistently.

During testing, some fields with invalid data types were automatically processed, while others were rejected with a validation error.

The API documentation does not describe how type mismatches should be handled.

## Test Scenarios

### Scenario 1 — `totalprice` as string

**Request**

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

**Observed behaviour**

- Request was accepted.
- `totalprice` was stored as `null`.

---

### Scenario 2 — PATCH with `totalprice` as string

**Request**

```json
{ "totalprice": "111" }
```

**Observed behaviour**

- Request was accepted.
- `totalprice` was stored as `null`.

---

### Scenario 3 — `depositpaid` as string

**Request**

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

**Observed behaviour**

- Request was rejected with `400 Bad Request`.

## Expected Behaviour

The API documentation should clearly define how incorrect JSON field types are handled.

Possible expected behaviours include:

- rejecting requests with invalid field types;
- automatically converting compatible values;
- applying another documented validation strategy.

The behaviour should be consistent across similar fields.

## Notes

This observation does not classify the behaviour as a defect.

Some APIs intentionally perform automatic type conversion, while others strictly validate request payloads.

Without documented validation rules, the observed behaviour is recorded as an implementation detail rather than a confirmed bug.