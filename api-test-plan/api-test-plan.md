# Master Test Plan — Restful-Booker

Base URL: https://restful-booker.herokuapp.com/
API Documentation: https://restful-booker.herokuapp.com/apidoc/index.html

Restful-Booker is a Create-Read-Update-Delete Web API with authentication features,intentionally loaded with a number of bugs for testing practice. The API comes pre-loaded with 10 records and resets itself every 10 minutes back to that default state (per the API homepage).

## Objective
- Verify that CRUD operations work as planned;
- Verify that authorization is required for PUT and DELETE;
- Validate that all status codes are correct;
- Validate that the structure of the response meets expectations.

## Scope
In scope: 
1. GET /ping
2. POST /auth
3. GET /booking 
4. GET /booking/{id} 
5. POST /booking
6. PUT /booking/{id}
7. PATCH /booking/{id}
8. DELETE /booking/{id}

Out of scope: 
- performance testing (no SLA for the sandbox);
- security beyond basic authorization;
- UI - as there is no UI in the project.

## Test Approach
The testing process follows this flow: Checklist → Test cases → Postman collection → Automated run via Newman → Defect reports.

**Test design techniques applied in this project:**

- Equivalence Partitioning — grouping inputs into classes treated the same way, testing one representative per class.
Example: for GET /booking/{id}, the classes are valid existing id and invalid/non-existing id.

- Boundary Value Analysis — testing at the edges where defects cluster. Example: for totalprice, I test minimum valid value / zero / negative value; for dates, I test today, past date, and edge dates around allowed range.

- Negative testing — verifying behavior with invalid input Example: PUT without token → expected 401 Unauthorized or 403 Forbidden (to be confirmed during execution, as the documentation does not specify the error code).

- State transition — verifying a resource lifecycle. Example: create a booking → update it → retrieve it → delete → verify it can no longer be found (404 Not Found).


## Test Environment
- Public sandbox on Heroku
- Tools: Postman, Newman
- Test data: 10 preloaded bookings; data resets every 10 minutes.

## Entry & Exit Criteria 
Entry criteria:
- API is reachable (GET /ping returns 201 Created).
- API documentation is available and up to date.
- Valid credentials are provided.

Exit criteria:
- All planned test cases have been executed.
- All defects have been logged and verified.
- No open blocker/critical defects

## Risks 
| Risk | Impact | Mitigation |
|---|---|---|
| Data resets every 10 minutes | Test data may be lost during execution, leading to unstable or non-repeatable results. | Design self-sufficient test cases that create required data before validation. |
| Rate limiter on too many requests | API responses may be throttled, which can affect test execution and reporting. | Respect rate limits by pacing requests and minimizing redundant calls. |

## Severity & Priority 
**Severity** (technical impact):
- ⛔️ Blocker — the most severe; the endpoint does not work at all / data loss occurs.
- 🔴 Critical — a key function is broken, for example authorization bypass.
- ⚠️ Major — works incorrectly with a noticeable impact.
- 🟡 Minor — a small issue with low impact.

**Priority** (fix urgency): High / Medium / Low