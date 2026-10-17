# API design

## Request schemas and OpenAPI

Define each request DTO once as a Zod schema and register it with
`registerZodSchema` from `apps/api/src/openapi/zod-registry.ts`. The helper
converts that schema to JSON Schema, registers it with Fastify for request
validation, and retains the same component for OpenAPI generation. Route
options reference the registered component (`{ $ref: "CreateWidgetBody#" }`)
instead of repeating a handwritten JSON Schema.

This keeps runtime acceptance rules and published API documentation together.
Maintaining separate Zod and JSON Schema definitions is tempting because it
looks explicit at each call site, but duplicated constraints drift as routes
change. The route registration guard makes an inline or unknown request schema
fail during app setup, so CI catches the mismatch before deployment.

Use shape constraints at the HTTP boundary. Rules that depend on domain state
or business policy stay in the application or domain layer.

## Error responses

Errors should use the shared API error envelope and stable error codes. Do not
return stack traces or internal exception messages to clients. See
`apps/api/src/http/error-response.ts` for the transport boundary.
