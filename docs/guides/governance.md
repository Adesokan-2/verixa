# Governance policy versioning

Policies are edited only while `draft`. Publishing creates an immutable
`PolicyVersion` snapshot and increments the policy version; later edits create
a new draft rather than mutating a published snapshot. This append-only choice
lets an audit reader reconstruct which rules were in force at an earlier point.
Published policies can be archived, but their version history remains
queryable in publication order.
