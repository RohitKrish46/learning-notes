# API Design Reading Notes

## Overview

API design defines how clients interact with backend services by choosing a protocol, modeling resources and operations, and specifying how data is passed and returned in requests and responses. In system design interviews, focus on selecting the appropriate API style (REST, GraphQL, RPC), modeling resources/actions clearly, handling pagination and versioning, and addressing security with authentication, authorization, and rate limiting.

## 1) API Types

### REST (default choice)

- Uses standard HTTP methods (GET, POST, PUT, PATCH, DELETE) on resource URLs for CRUD-style operations, mapping naturally to web/mobile backends.
- Resource modeling: design endpoints around nouns (entities), not verbs (actions), e.g., events, venues, tickets, bookings.
- Example endpoints:
    - `GET /events` — list events
    - `GET /events/{id}` — event details
    - `GET /events/{id}/tickets` — tickets for an event
    - `POST /events/{id}/bookings` — create a booking
    - `GET /bookings/{id}` — booking details

### HTTP methods

- **GET:** read-only retrieval; safe and non-mutating.
- **POST:** create new resource; not idempotent (replays create duplicates).
- **PUT:** replace full resource; idempotent.
- **PATCH:** partial update; idempotent.
- **DELETE:** remove resource; idempotent.

### Passing data

- **Path parameters:** required identifiers in the URL (e.g., `/events/123`) to target a specific resource.
- **Query parameters:** optional filters, sorts, pagination, e.g., `/events?city=NYC&date=2024-01-01`, with `?` for first and `&` for subsequent params.
- **Request body:** complex data for create/update (e.g., booking details), including arrays and sensitive content not suited for URLs.

### Returning data

- **Status code** indicating outcome (e.g., 200/201/400/404/500).
- **Response body**, typically JSON, containing the requested or resulting data.

### GraphQL

- Single endpoint with a query language enabling clients to specify exactly the fields needed, avoiding over/under-fetching.
- Useful when clients have diverse data needs (e.g., mobile vs. web) and want to minimize roundtrips and payload size.
- Example query idea:
    - Query `event(id)` — request event fields and nested venue/tickets fields in one request.
- Schema design:
    - Define types (Event, Venue, Ticket) and Query fields (event, events) with arguments and pagination controls.
- Common pitfall:
    - N+1 query problem when resolving nested fields; mitigate with batching/dataloader patterns.

### RPC (e.g., gRPC)

- Procedure-oriented calls over HTTP/2 with binary serialization (Protocol Buffers), well-suited for internal microservice communication.
- Natural for actions like `checkPermission` or `createBooking` without forcing resource semantics.
- Strong typing via `.proto` contracts enables generated client/server code and compile-time safety.
- When to use:
    - Performance-critical internal calls, type safety, service-to-service communication, streaming requirements (bidirectional streams).

## 2) Common API Patterns

### Pagination

- **Offset-based:** simple page/limit pattern; easy but can be inefficient or inconsistent on large/fast-changing datasets.
- **Cursor-based:** return a stable cursor (e.g., `next_cursor`) to fetch the next page; preferred for scalability and consistency in large lists.

### Versioning strategies

- **URL versioning:** `/v1/events`, `/v2/events`; explicit and easy to route and reason about.
- **Header versioning:** `Accept-Version` or `API-Version` headers; cleaner URLs but less obvious to developers and harder to test in browsers/tools.

## 3) Security Considerations

### Authentication vs Authorization

- **Authentication:** verify identity (who is calling).
- **Authorization:** verify permissions (what the caller can do).

### API keys vs JWT

- **API Keys:** simple bearer secret used in Authorization header; common for server-to-server or public API access control.
- **JWT (JSON Web Tokens):** signed tokens with claims (`user_id`, `role`, `exp`) for stateless auth and role checks across services.

### RBAC (Role-Based Access Control)

- Define roles (e.g., customer, venue_manager, admin) with allowed actions; enforce per-endpoint checks (ownership or role-based rules).

### Rate limiting and throttling

- **Per-user limits:** e.g., 1,000 requests/hour for authenticated users.
- **Per-IP limits:** lower caps for unauthenticated access.
- **Endpoint-specific limits:** e.g., tighter limits on sensitive actions like bookings to prevent abuse.

## 4) Practical Guidance for Interviews

- Choose **REST** by default; switch to GraphQL if flexible data fetching is required, or to RPC for internal microservice calls requiring high performance and strong typing.
- Model resources clearly and use correct HTTP semantics; ensure idempotency where required (PUT/PATCH/DELETE) and acknowledge POST duplication risks.
- Use path parameters for resource identity, query parameters for filtering/sorting/pagination, and request bodies for complex input.
- Prefer cursor-based pagination for large datasets; expose `next_cursor` for sequential retrieval.
- Version deliberately; URL versioning is the simplest to communicate and route, while header-based can be used for cleaner paths.
- Address security explicitly: authentication mechanism (API key/JWT), authorization model (RBAC), and rate limits per user/IP/endpoint.
