# API Design

The REST API is a transport boundary around application use cases. Routes parse HTTP input, call a use case, and translate its result into an HTTP response. They do not own business rules or data access.

## Error response contract

Every API error uses this shape:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed.",
    "details": {
      "fields": {
        "body.email": ["must be a valid email"]
      }
    },
    "requestId": "req-..."
  }
}
```

`details` is optional and carries structured, client-actionable information. Request validation uses `details.fields`; other failures omit details unless the API defines a similarly safe structure. `requestId` is always present and matches the identifier in server logs, so a client can report a failure without receiving stack traces, SQL details, or other internal data.

The `code` comes from the explicit registry in `apps/api/src/http/error-code-registry.ts`. Clients may branch on this stable value; they should not branch on `message`, which is written for people and may be clarified. Domain errors are mapped through that registry. Unknown exceptions are logged with their request context and returned as a sanitized `INTERNAL_ERROR`.

Common codes include `VALIDATION_ERROR` (400), `AUTHENTICATION_FAILED` (401), `FORBIDDEN` (403), `NOT_FOUND` (404), `CONFLICT` (409), `RATE_LIMITED` (429), and `INTERNAL_ERROR` (500). Domain-specific failures keep their registered code and status rather than being collapsed into one generic response.

The API uses one global Fastify error handler instead of per-route `try/catch` mappings. This ensures framework errors, validation failures, and uncaught exceptions share the same response shape. Keeping domain-to-HTTP mappings in one table also makes adding a new domain failure an explicit API decision rather than an accidental result of a generic catch-all.

The response schema is exported as `ErrorResponse` for reuse as the shared OpenAPI component. It intentionally describes the common envelope; operation-specific responses can reference it without redefining the envelope or drifting on required fields.
