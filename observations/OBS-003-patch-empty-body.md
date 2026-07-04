# OBS-003 — PATCH empty body behaviour

**Status:** Observation  
**Environment:** Restful-Booker, Postman  
**Endpoint:** `PATCH /booking/{id}`  
**Related test case:** `TC-PATCH-008`

## Summary

The `PATCH /booking/{id}` endpoint accepts an empty JSON object (`{}`) and returns `200 OK`.

No validation error is returned, and the request completes successfully.

The API documentation does not specify whether an empty PATCH request is supported or how it should be handled.

## Preconditions

- Restful-Booker API is available.
- Postman is used to send the request.
- A valid booking exists.
- A valid authentication token is available.

## Test Scenario

**Request** ```http PATCH /booking/{id} ```

**Headers**

```http
Content-Type: application/json
Cookie: token=<valid_token>
```

**Request Body**

```json
{}
```

## Observed Behaviour

- Status code: `200 OK`
- The request is processed successfully.
- No validation error is returned.
- No visible changes are made to the booking data.

## Expected Behaviour

The expected behaviour is not defined in the API documentation.

Possible valid implementations include:

- treating the request as a successful no-op and returning `200 OK`;
- rejecting the request because no fields were provided for update;
- returning another documented response indicating that no update was performed.

The behaviour should be clearly documented.

## Notes

This observation is not classified as a defect.

Many REST APIs allow an empty PATCH request and treat it as a no-op, while others reject it as an invalid request.

Without documented expected behaviour, this case is recorded as an observation rather than a bug.