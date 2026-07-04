# OBS-001 — XML response behaviour

**Status:** Observation  
**Environment:** Restful-Booker, Postman  
**Endpoints:** `GET /booking/{id}`, `POST /booking`  
**Related test case:** `TC-GET-BOOKING-002`

## Summary

Different API endpoints handle the `Accept: application/xml` request header differently.

During testing, `POST /booking` returned an XML response as requested, while `GET /booking/{id}` returned a response with `Content-Type: text/html`.

It is unclear whether this behaviour is intentional or a limitation of the implementation, as the API documentation does not explicitly describe XML support for each endpoint.

## Test Scenario

### POST /booking

**Request header** ``` Accept: application/xml ```

**Observed behaviour**

- Response returned in XML format.
- XML response was rendered correctly.

---

### GET /booking/{id}

**Request header** ``` Accept: application/xml ```

**Observed behaviour**

- Response returned with: ``` Content-Type: text/html ```

- XML response was not returned.

## Expected Behaviour

The API documentation should clearly specify whether XML responses are supported for each endpoint.

If XML is supported, endpoints should respond consistently when the client requests: ``` Accept: application/xml ```

## Notes

This observation does not necessarily indicate a defect.

The behaviour may be intentional, undocumented, or implementation-specific.

Further clarification from the API documentation or maintainers would be required before classifying it as a bug.