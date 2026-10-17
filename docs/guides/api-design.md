# API Design

## Request validation

Routes declare schemas for every accepted body, path parameter, and query
parameter. Fastify validates these inputs before it calls the handler, so bad
requests cannot reach a use case. Keep schemas at the HTTP boundary: they
validate request shape, while domain and application layers enforce business
rules.

The API registers a route guard during app construction. It rejects write
routes without a body schema, parameterized paths without a params schema, and
the assignment listing route without its query schema. This makes missing
validation fail during CI app setup instead of allowing an unvalidated route
to ship. Validation failures use the shared `VALIDATION_ERROR` response with
field-level details.
