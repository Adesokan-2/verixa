# Governance membership

Organization roles are scoped to one organization and are not platform RBAC.
`owner` and `admin` may manage memberships; only an owner may grant or retain
the owner role. A membership change is evaluated against the current owner
count so the last owner cannot be removed or demoted. Use cases perform
authorization and persistence orchestration, while the aggregate enforces the
invariants, making retries safe and auditable at the repository boundary.
