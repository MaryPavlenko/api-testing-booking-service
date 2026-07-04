# BUG-008 — GET /booking/{id} does not honor the Accept header

**Severity:** Minor  
**Priority:** Low  
**Status:** Open  
**Environment:** Restful-Booker, Postman  
**Endpoint:** `GET /booking/{id}`  
**Related test case:** `TC-GET-BOOKING-002`

## Description

The `GET /booking/{id}` endpoint does not honor the `Accept: application/xml` request header.

Instead of returning the response in XML format, the endpoint responds with `Content-Type: text/html`, despite the client explicitly requesting XML.

## Preconditions

- Restful-Booker API is available.
- Postman is used to send the request.
- A booking with a valid ID exists.

## Steps to Reproduce

1. Send `GET /booking/{id}`.
2. Add the following request header:

```
Accept: application/xml
```

3. Observe the response headers and response body.

## Expected Result

- The endpoint returns the response in XML format.
- Response header:

```
Content-Type: application/xml
```

## Actual Result

- The endpoint returns:

```
Content-Type: text/html
```

- The requested XML representation is not returned.

## Impact

Clients relying on HTTP content negotiation cannot obtain the requested response format.

This makes the endpoint behaviour inconsistent and reduces interoperability with applications expecting XML responses.

## Additional Notes

`POST /booking` correctly supports XML responses when the client sends `Accept: application/xml`.

`GET /booking/{id}` behaves differently, suggesting inconsistent support for content negotiation across API endpoints.